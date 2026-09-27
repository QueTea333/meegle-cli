# Field value advancement: associated work item name → ID conversion

When the user provides the **work item name instead of the ID** for the `workitem_related_select` / `workitem_related_multi_select` field, follow the following process to convert and then write.

1. **Get the target constraints of the associated field**: From the field configuration returned by `workitem meta-fields`, extract its bound **target space** (`project_key`) and **target work item type** (`work_item_type_key`). If the configuration is not limited (can be associated with any type), the search will be performed within the current space by default.
2. **Search target work items by name**: Call `workitem query` to match by name within the target space and type range. Example MQL:
   ```sql
SELECT `work item ID`, `name` FROM `target space`.`target type` WHERE `name` = 'name given by user'
   ```
If there are no results for exact matching, use fuzzy search like '%keyword%' instead.
3. **Disambiguation**:
- Unique result → Get the work item ID directly
- Multiple results → list all matches (ID + name + status) for user confirmation
- Zero results → Prompt user "No work item named XXX found, please confirm the name or provide the ID directly"
4. **Writing format**:
- `workitem_related_select` → pass in a single ID string
- `workitem_related_multi_select` → Pass in stringified ID array
- Different spaces may require string or numeric format. If type verification fails, switch the format immediately and try again.
5. **Circular reference protection**: The current work item's own ID must be checked before writing, and **it is prohibited to write its own ID into related fields**, otherwise it will trigger an `exists loop` (circular reference) error.
