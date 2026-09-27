# Auth Guard (must be executed before all business commands)

## Trigger conditions

- **Active login**: Users say "log in Meegle", "connect to Feishu project", "login meegle", etc.
- **Passive interception**: When the user requests any Meegle business operation (query to-do, check work items, create tasks, etc.), Auth Guard will be executed first.
- **URL trigger**: The user sends the Feishu project/Meegle URL. Processing flow:
1. First call `url decode` to get the structured fields (`url_kind`, `host`, `simple_name`, `work_item_id`, etc.). **It is forbidden** to intercept the path segment from the URL as a parameter. For field meanings and kind branches, see [url-kinds.md](url-kinds.md).
2. Save `$url_host` = response.host, `$target_host = $url_host`, `$url_kind`, `$simple_name`, `$work_item_id`. `$url_host` only represents the site to which the original link belongs, and `$target_host` represents the site that must be connected to this round of business. Both of them must not be overwritten by subsequent login status.
3. Execute Auth Guard (from STEP 1 below).
4. After successful login, press the `$url_kind` branch:
- `workitem_detail` → `project search` to get the authoritative `$project_key`, and then `workitem get` to query the details
- `workitem_homepage` / `view_*` / `unknown` and other non-detail pages → follow the guidelines of url-kinds.md to refuse or ask questions
- Other kinds → refer to url-kinds.md for corresponding processing methods

Follow the following STEP sequence. The GOTO at the end of each STEP indicates the next step, and jumps are strictly followed.

Initialize variables before entering STEP 1:

- URL triggering → Keep the `$url_host` and `$target_host` resolved above.
- Non-URL trigger → SAVE `$url_host = null`; when the calling process explicitly requires access to a specific site, `$target_host` must be passed in for this request (such as an explicit domain name in the link generation process), otherwise SAVE `$target_host = null`. Inheriting values ​​from the previous request is prohibited.
- Save `$profile_args` as an argument array: user explicitly selected profile → `profile_args=(--profile "$profile")`; not explicitly selected → `profile_args=()`. All the following Auth Guard commands and STEP DONE business commands must reuse the same array, and switching midway is prohibited.

---

### STEP 1 — Check login status

```bash
meegle "${profile_args[@]}" auth status --format json
```

Return value example:
- Logged in: `{ "authenticated": true, "host": "meegle.com", "source": "token_store", "expires_in_minutes": 42 }`
- Not logged in and has host: `{ "authenticated": false, "host": "meegle.com", "source": null, "expires_in_minutes": null }`
- Not logged in and no host: `{ "authenticated": false, "host": null, "source": null, "expires_in_minutes": null }`

Parse the return value and save the variable:
- `$authenticated` = response.authenticated
- `$auth_host` = response.host

**Target host consistency check**: Executed when `$target_host` is not empty. Before comparison, convert the domain names of `$target_host` and `$auth_host` to lowercase and remove the `.` at the end. The ports must be consistent.

- IF `$target_host != null` AND `$auth_host != null` AND The two are inconsistent → SEND "The current login site is `$auth_host`, but the target belongs to `$target_host`. Please switch or specify the profile corresponding to the target site and try again; no subsequent query was executed this time."; STOP
- IF `$auth_host != null` → SAVE `$host = $auth_host`
- IF `$auth_host == null` AND `$target_host != null` → SAVE `$host = $target_host`
- Otherwise → SAVE `$host = null`

**Jump:**
- IF `$authenticated == true` → GOTO STEP DONE
- IF `$host != null` → GOTO STEP 2
- IF `$host == null` → GOTO STEP HOST

---

### STEP HOST — Select site

ASK user (waiting for user reply):

> Which site do you want to connect to?
> 1) Feishu Project (project.feishu.cn)
> 2) Meegle (meegle.com)
> 3) Custom domain name (please enter the domain name directly)

SAVE `$host` from user reply → GOTO STEP 2

---

### STEP 2 — OAuth login

```bash
meegle "${profile_args[@]}" auth login --host "$host"
```

The command will automatically open the browser to complete OAuth authorization. Wait for the command to complete execution.

**Jump:**
- IF command successful (exit code 0) → GOTO STEP OK
- IF command failed → SEND "OAuth login failed, please check the error message or manually re-execute the above login command in the terminal", STOP

---

### STEP OK — Notification of successful login

SEND to user: "Login successful!"

> ⚠️ This message **must be sent alone** and should not be merged into the same reply with subsequent business query results. Users need to see changes in authorization status as soon as possible.

→ GOTO STEP DONE

---

### STEP DONE — Execute business commands

Auth passed, perform the action requested by the user. Each business command continues to use the same `$profile_args` saved before entering STEP 1.

## Error handling

- If bash returns `command not found` or npx is not available, prompt the user to install Node.js 18+.
- If the OAuth login fails, the user is prompted to manually re-execute the above login command in the terminal.
