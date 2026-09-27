# Attachment field

## Core Boundary

Work item attachment has three independent stages: **Upload file to object storage → Write file object to target field → Read back target field for acceptance**. Successful uploading does not mean successful field writing; only when all three steps are completed can the user be informed that the attachment has been written.

The bottom layer of attachment uploading/downloading is divided into two steps: first call `attachment prepare-upload` / `attachment prepare-download` to apply for a signed object storage URL, and then make one or more direct HTTP connections with the object storage.

The return result of `attachment prepare-upload` may contain `target_unit_code`; when initiating an upload to the object storage by yourself, if the value is not empty, `x-target-unit` must be added to each POST (including all uploaded fragments), and the value of `target_unit_code` will be used as it is; if it is missing or empty, the request header will not be added. The return result of `attachment prepare-download` may also contain `target_unit_code`; when downloading by yourself, if the value is not empty, the same `x-target-unit` must be added to each GET (including all downloaded fragments), and the value of `target_unit_code` will be used as it is; if it is missing or empty, the request header will not be added.

Meegle CLI has built-in `attachment +upload` / `attachment +download` one-click encapsulation, combining the two steps into one command; when step-by-step control is required in the script, the above prepare command can also be adjusted separately.
## Write front access control

### 1. Verify local files

- Confirm each path named by the user one by one: the file exists, is a normal file, is readable, and the basename is consistent with the user's requirements.
- Stop immediately when the local file does not exist and ask the user to add or confirm. **It is prohibited to automatically replace other files with the same name, and it is prohibited to replace file extensions without authorization** (for example, when requesting `ad.png`, pass `ad.mp4` instead).

### 2. Target field disambiguation

Each time `workitem meta-fields` must be called for **current space + current work item type**, use `field_types=["file","multi-file"]`, `page_num=1` to query attachment fields in real time; if `pagination.has_more=true` is returned, continue to increment the page number until all pages are collected before disambiguating. Then identify goals in the following order:

1. The user explicitly gives the field key: only exact key matching is accepted.
2. The user explicitly gives the field name: only exact field name matches are accepted; fuzzy results can only be used to display candidates. If there are still multiple candidates after exact matching of field names, list the key and type of each candidate and let the user select by key.
3. The user only says "attachment": If there is only one candidate, you can use it directly; if there are multiple candidates, list the field name, key, and type and let the user choose.

It is forbidden to select the first candidate, to reuse fields from historical sessions, and to treat `attachment` or `multi_attachment` as fixed keys. Record the `target_field_key`, field name and `field_type` before execution. The same key must always be used for subsequent uploading, writing and acceptance.

The existence of a certain field in metadata can only prove that the API can recognize it, but it cannot prove that the field has been put into the user's current details page layout or is visible in the UI. When there is no layout query capability, the conclusion should be clearly limited to "the API target field is written and read back successfully."

### 3. Differentiate field protocols

`file` and `multi-file` are not unconditionally interchangeable protocols:

- `multi-file` uses an array of stringified file objects to support maintaining multiple attachments according to the following protocol.
- `file` may be a legacy or single-attachment field; only operate on its proven capabilities if actual writes and readbacks of the current space have verified its behavior. Do not claim that it supports multiple attachments based solely on field names or historical experience.
- The file object read back by `workitem get` may have more `uid`, `url` or other server-side fields than the upload result. These unknown fields must be retained intact during subsequent merges, and the old object cannot be downgraded to a simplified object with only `name/type/size/fileToken`.

## End-to-end writing protocol

After the upload is successful, map the return value to a field object:

```json
{"name":"a.pdf","type":"application/pdf","size":"12345","fileToken":"<token>"}
```

Among them, `type` comes from the `mime_type` of the upload result, `fileToken` comes from `file_token`, and `size` is written as a decimal string; the entire array must be JSON.stringify again as `field_value`.

### Single attachment

1. Upload the file through the disambiguated `target_field_key` and obtain the real `file_token`.
2. When creating a work item and the target field is currently empty, or the user explicitly requires replacement, you can adjust `workitem create` / `workitem update` to directly write to a single-element array.
3. When adding an attachment to an existing `multi-file` field, you must first call `workitem get`: record the number of baselines, keep the current complete array as it is, add new objects, and then call `workitem update`. Do not overwrite the old array just because you are adding only one new file this time. `file` fields are still treated according to the compatibility rules above.
4. Immediately call `workitem get` to read back **the same target field**, and perform post-write acceptance based on file name and quantity. The expected quantity under append semantics is "baseline quantity + 1"; you cannot just check the upload return value, nor read back another attachment field.

### Write multiple attachments to the same `multi-file` field

The reliable path is to append single attachments one by one after they are dropped into the library. Do not use "pass in multiple simplified objects at once during creation" as the default protocol:

1. Upload the created scene first and write the first attachment with `workitem create` to get the work item ID; the remaining attachments will be processed one by one using this ID. Existing work items start directly from the next step.
2. Before appending a file, call `workitem get` to read the current complete array of `target_field_key`.
3. Upload a new file; keep the old objects in the current array as they are (including `uid`, `url` and unknown fields), and only add this new file object at the end of the array.
4. Stringify the entire new array and call `workitem update` to cover the target field; then read back again to confirm that this round of new files has appeared.
5. Repeat 2–4 for remaining files. Finally, compare the multiset and number of file names requested by the user: equal to the total number of requests under creation semantics, and equal to "baseline number + number of successful additions" under append semantics; tokens may be re-signed by the server, and complete equality of tokens cannot be used as the only acceptance condition.

If writing multiple simplified objects at one time triggers `unique` verification, switch directly to the above-mentioned append protocol one by one; you must not delete attachments that have been successfully dropped and then blindly retry. Updates have overwriting semantics, and not reading and retaining the old array first will cause existing attachments to be lost.

## Post-writing acceptance list

- What is read back is the disambiguated and actually written `target_field_key`.
- Single attachment: The target file name exists and the number of target fields is as expected.
- Multiple attachments: Every filename requested is present, duplicate files are counted as intended by the user, and the total is as expected.
- When the API acceptance is passed but the page layout cannot be verified, it will be truthfully stated that the UI visibility has not been verified, and it will not be packaged as UI success.

## attachment prepare-upload
Apply to upload signature. `work_item_id` and `work_item_type` **Choose one of the two required**: Pass `work_item_id` for an existing work item; "Upload attachments simultaneously when creating a work item" scenario passes `work_item_type`, when both are transmitted simultaneously, `work_item_id` takes precedence.

| Parameters | Type | Required | Description |
|------|------|------|------|
| --project-key | string | yes | space key |
| --resource-type | number | Yes | Attachment scenarios: 13=Comment attachment / 14=Comment image / 15=Work item attachment field / 16=Rich text field image |
| --file-name | string | yes | attachment name |
| --mime-type | string | Yes | MIME type |
| --size | number | Yes | The total size of the file (bytes); the backend will use this to determine whether to upload a single upload or split it |
| --work-item-id | string | Choose one of two | Existing work item ID |
| --work-item-type | string | Choose one of the two | Work item type (only "Create work item and upload attachment simultaneously" scenario) |
| --field-key | string | Condition | `resource_type=15/16` required, 13/14 not filled in |

## attachment prepare-download
Request download signature. `file_url` is an opaque reference returned by other commands (such as the attachment field value of `workitem get`, the attachment link in `comment list` comments, and the attachment reference in rich text). **Do not** manually splice it.

| Parameters | Type | Required | Description |
|------|------|------|------|
| --project-key | string | yes | space key |
| --work-item-id | string | yes | work item ID |
| --file-url | string | Yes | Attachment URL (from attachment field, comment, or rich text) |

## attachment +upload
End-to-end upload: CLI locally strings `attachment prepare-upload` with object storage's signed HTTP POST, returns `file_token` and file metadata, which can be directly fed to the attachment field of `workitem create` / `workitem update` / `comment add`. **Meegle CLI only**.

| Parameters | Type | Required | Description |
|------|------|------|------|
| `<source-path>` (positional parameter) | string | yes | local file path |
| --resource-type | string | Yes | 13/14/15/16, the same meaning as `attachment prepare-upload` |
| --project-key | string | yes | space key |
| --work-item-id | string | Choose one of two | Existing work item ID |
| --work-item-type | string | Choose one of two | Create the work item type of the scenario |
| --field-key | string | Condition | Required when resource_type=15/16 |
| --filename | string | No | Override the file name sent to the backend (default is local basename) |
| --content-type | string | No | Override MIME type (detected by extension by default, `application/octet-stream` is not recognized) |

## attachment +download
End-to-end download: The CLI locally connects `attachment prepare-download` with the signature HTTP GET of the object storage, and uses `.partial` temporary file + atomic rename to disk, so that no half-broken files will be left when it fails. **Meegle CLI only**.

| Parameters | Type | Required | Description |
|------|------|------|------|
| `<file-url>` (positional parameter) | string | Yes | Attachment URL (from attachment field, comment, or rich text) |
| --project-key | string | yes | space key |
| --work-item-id | string | yes | work item ID |
| --output | string | yes | local landing path |
| overwrite | bool | no | whether to overwrite the target if it already exists (default false) |
