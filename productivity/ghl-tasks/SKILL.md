---
name: ghl-tasks
description: "GoHighLevel (GHL) integration: create tasks, appointments, and social‑media posts for sub‑accounts"
version: 0.1.0
platforms: [linux, macos, windows]
author: Hermes Agent
---

# GoHighLevel Tasks & Appointments Skill

This skill provides helper tools to interact with the GoHighLevel (GHL) REST API for sub‑accounts you have access to (e.g., `getzot.com`, `FLSource.com`, `inc2biz.com`). It wraps the most‑common endpoints:

- **Create a task** (`ghl_create_task`)
- **Create an appointment** (`ghl_create_appointment`)
- **Create a social‑media post** (`ghl_create_social_post`)

All tools require a valid **Private Token** (or OAuth token) for the target sub‑account. Store the token in your Hermes secrets store (`.env` or via `hermes auth`) under the key `GHL_<SUBACCOUNT>_TOKEN`, e.g.:

```
GHL_GETZOT_TOKEN=pat_xxxx…
GFS_SOURCE_TOKEN=pat_yyyy…
INC2BIZ_TOKEN=pat_zzzz…
```

The skill will automatically read the appropriate token based on the `subaccount` argument you provide.

## Tools

### `ghl_create_task`
Create a task in a GHL sub‑account.

**Parameters**
- `subaccount` (string): Identifier used to look up the token, e.g. `getzot`, `FLSource`, `inc2biz`.
- `title` (string): Task title.
- `description` (string, optional): Task description.
- `due_date` (string, optional): ISO‑8601 timestamp (e.g., `2025-09-01T10:00:00Z`). If omitted, task is set as “no due date”.
- `assignee_id` (string, optional): GHL user ID to assign the task to. If omitted, assigned to the token owner.

**Returns**
JSON with `success`, `task_id`, and the full response payload.

### `ghl_create_appointment`
Schedule an appointment (calendar event) in a GHL sub‑account.

**Parameters**
- `subaccount` (string): Identifier for token lookup.
- `title` (string): Appointment title.
- `calendar_id` (string): GHL calendar ID where the appointment should be placed.
- `start_time` (string): ISO‑8601 start timestamp.
- `end_time` (string): ISO‑8601 end timestamp.
- `contact_id` (string, optional): GHL contact ID to associate with the appointment.
- `location` (string, optional): Physical or virtual location.
- `notes` (string, optional): Additional notes.

**Returns**
JSON with `success`, `appointment_id`, and the full response payload.

### `ghl_create_social_post`
Create a scheduled social‑media post via GHL’s Social Planner.

**Parameters**
- `subaccount` (string): Identifier for token lookup.
- `platform` (string): One of `facebook`, `instagram`, `twitter`, `linkedin`, `google_my_business`.
- `message` (string): Post copy.
- `media_urls` (list of strings, optional): URLs to images/videos.
- `scheduled_at` (string, optional): ISO‑8601 timestamp for when to publish. If omitted, posts immediately.
- `account_id` (string, optional): GHL social account ID to post to (defaults to first connected account for the platform).

**Returns**
JSON with `success`, `post_id`, and the full response payload.

## Helper Functions (internal)

The skill internally loads tokens from the environment:

```python
import os

def _get_token(subaccount: str) -> str:
    key = f"GHL_{subaccount.upper()}_TOKEN"
    token = os.getenv(key)
    if not token:
        raise ValueError(f"Missing GHL token for subaccount '{subaccount}'. Set env var {key}")
    return token
```

All HTTP requests use the base URL `https://services.leadconnectorhq.com` and include the header `Authorization: Bearer <token>` and `Version: 2021-07-28` (as required by GHL API).

## Usage Example (via Hermes chat)

```bash
# Create a task for the getzot subaccount
hermes tools run ghl_create_task '{ "subaccount": "getzot", "title": "Follow up with lead", "due_date": "2025-09-15T09:00:00Z" }'

# Schedule an appointment on FLSource calendar
hermes tools run ghl_create_appointment '{
  "subaccount": "FLSource",
  "title": "Demo call with ACME Corp",
  "calendar_id": "cal_abc123",
  "start_time": "2025-09-20T14:00:00Z",
  "end_time": "2025-09-20T14:30:00Z",
  "contact_id": "cont_xyz789"
}'

# Create a Facebook post for inc2biz
hermes tools run ghl_create_social_post '{
  "subaccount": "inc2biz",
  "platform": "facebook",
  "message": "Check out our new product launch! 🚀",
  "media_urls": ["https://example.com/image.jpg"],
  "scheduled_at": "2025-09-21T10:00:00Z"
}'
```

## Installation

1. Save the following files into `~/.hermes/skills/ghl-tasks/`:
   - `SKILL.md` (this file)
   - `scripts/ghl_tools.py` (see below)

2. Run `hermes skills install ghl-tasks` (or simply restart Hermes; the skill will be auto‑loaded on next session).

3. Ensure your GHL tokens are exported in your environment or added via `hermes auth`:

   ```bash
   hermes auth add custom --name GHL_GETZOT_TOKEN --value pat_xxxx…
   hermes auth add custom --name GHL_FLSOURCE_TOKEN --value …
   hermes auth add custom --name GHL_INC2BIZ_TOKEN --value …
   ```

## Script: `scripts/ghl_tools.py`

```python
#!/usr/bin/env python3
"""
Helper script for GHL tools exposed via the `ghl-tasks` skill.
Each function returns a JSON string suitable for direct consumption by Hermes.
"""
import json
import os
import requests
from typing import Any, Dict, List

BASE_URL = "https://services.leadconnectorhq.com"
API_VERSION = "2021-07-28"

def _headers(token: str) -> Dict[str, str]:
    return {
        "Authorization": f"Bearer {token}",
        "Version": API_VERSION,
        "Content-Type": "application/json",
    }

def _get_token(subaccount: str) -> str:
    key = f"GHL_{subaccount.upper()}_TOKEN"
    token = os.getenv(key)
    if not token:
        raise ValueError(f"Missing GHL token for subaccount '{subaccount}'. Set env var {key}")
    return token

def ghl_create_task(args: Dict[str, Any]) -> str:
    sub = args["subaccount"]
    payload = {
        "title": args["title"],
        "description": args.get("description", ""),
    }
    if args.get("due_date"):
        payload["dueDate"] = args["due_date"]
    if args.get("assignee_id"):
        payload["assignedTo"] = args["assignee_id"]

    url = f"{BASE_URL}/tasks/"
    resp = requests.post(url, json=payload, headers=_headers(_get_token(sub)))
    resp.raise_for_status()
    data = resp.json()
    return json.dumps({"success": True, "task_id": data.get("id"), "data": data})

def ghl_create_appointment(args: Dict[str, Any]) -> str:
    sub = args["subaccount"]
    payload = {
        "title": args["title"],
        "calendarId": args["calendar_id"],
        "startTime": args["start_time"],
        "endTime": args["end_time"],
    }
    if args.get("contact_id"):
        payload["contactId"] = args["contact_id"]
    if args.get("location"):
        payload["location"] = args["location"]
    if args.get("notes"):
        payload["notes"] = args["notes"]

    url = f"{BASE_URL}/calendars/{args['calendar_id']}/appointments"
    resp = requests.post(url, json=payload, headers=_headers(_get_token(sub)))
    resp.raise_for_status()
    data = resp.json()
    return json.dumps({"success": True, "appointment_id": data.get("id"), "data": data})

def ghl_create_social_post(args: Dict[str, Any]) -> str:
    sub = args["subaccount"]
    payload = {
        "platform": args["platform"],
        "message": args["message"],
    }
    if args.get("media_urls"):
        payload["mediaUrls"] = args["media_urls"]
    if args.get("scheduled_at"):
        payload["scheduledAt"] = args["scheduled_at"]
    if args.get("account_id"):
        payload["accountId"] = args["account_id"]

    url = f"{BASE_URL}/social/post"
    resp = requests.post(url, json=payload, headers=_headers(_get_token(sub)))
    resp.raise_for_status()
    data = resp.json()
    return json.dumps({"success": True, "post_id": data.get("id"), "data": data})

# ----------------------------------------------------------------------
# Tool registry hooks (Hermes will auto‑discover functions named *_tool)
# ----------------------------------------------------------------------
def ghl_create_task_tool(args: Dict[str, Any]) -> str:
    return ghl_create_task(args)

def ghl_create_appointment_tool(args: Dict[str, Any]) -> str:
    return ghl_create_appointment(args)

def ghl_create_social_post_tool(args: Dict[str, Any]) -> str:
    return ghl_create_social_post(args)
```

Make the script executable:

```bash
chmod +x ~/.hermes/skills/ghl-tasks/scripts/ghl_tools.py
```

## Verification

After installing/reloading the skill:

```bash
hermes tools list | grep ghl
# Should show ghl_create_task, ghl_create_appointment, ghl_create_social_post
```

You can now run any of the tools as shown in the usage examples.

---
*Tip*: If you encounter authentication errors, double‑check that the environment variable name matches `<SUBACCOUNT>_TOKEN` in all caps (e.g., `GHL_GETZOT_TOKEN`). You can also store tokens via Hermes' built‑in secret manager (`hermes auth add custom …`) and they will be injected into the process environment automatically.