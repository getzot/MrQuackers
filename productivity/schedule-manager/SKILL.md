---
name: schedule-manager
description: "Run GetZot/Inc2Biz/FLSource ops via GoHighLevel API: tasks, appointments, contacts."
version: 2.0.0
platforms: [linux, macos, windows]
author: Hermes Agent
tags: [gohighlevel, ghl, schedule, tasks, calendar]
---

# Schedule Manager — GoHighLevel Operations for GetZot / Inc2Biz / FLSource

Verified working setup (tested 2026-08-26). Use this as the playbook for all
scheduling/task/project-management requests from Tim Casiglia & Tony Williams.

## Environment

- API base: `https://services.leadconnectorhq.com`
- Required headers on every call:
  - `Authorization: Bearer <token>`
  - `Version: 2021-07-28`
  - `Accept: application/json`
- Tokens live in `~/.hermes/.env` (load with `set -a; source ~/.hermes/.env; set +a` in bash):
  - `GHL_GETZOT_PRIVATE`, `GHL_INC2BIZ_PRIVATE`, `GHL_FLSOURCE_PRIVATE`, `GHL_AGENCY_PRIVATE`
  - Agency token can read all sub-accounts via `/locations/search`.

## Sub-Account (Location) IDs

| Business | locationId | Timezone |
|---|---|---|
| GetZot | `lUIF0sXBf8GWAoWAXWPV` | America/New_York |
| Inc2Biz | `6wqc4LNv1CdjKCilqdxP` | US/Eastern |
| FLSource | `5uMoM3LyGUOToGpsWVWo` | US/Eastern |

## User IDs

| Person | userId | Notes |
|---|---|---|
| Tim Casiglia | `xYDfgoeRX5LgpY14VZ0H` | tim@getzot.com, agency admin, all 3 accounts |
| Tony Williams | `LRImLsHaSobjTIp8agyE` | tony@getzot.com, agency admin, all 3 accounts |
| Tim (Inc2Biz local) | `4ugxGjgbvG2Pwj8bynQc` | tim@inc2biz.com |
| Tony (Inc2Biz local) | `t6VAswfPOqy3xnHVnzjF` | tony@inc2biz.com |

## Calendars

GetZot: Free Consult `c6wEDj8ijw1yJCWjml9A`, Onboarding `ewIEn1rTZAh1at5d9GPm`,
Booking Request `ub4YQHX38VqYiB3ZZIrn`.
Inc2Biz: Startup Consult `dW4pWizO1wee20GyZS0a`, Admin `ude9D063qa9J5rOuIxvn`.
FLSource: Admin `0Ymerxk3VgLH99K71uug`, Tim personal `nfpOdXSc45dHb8I0SEPA`,
Tony personal `0dBRY1caO51L5uyhQZAP`, Digital Network Night `MnF5X47Csm6dOjlxAN37`.

## Key Endpoints

List users:
```
GET /users/?locationId=<loc>
```
List calendars:
```
GET /calendars/?locationId=<loc>
```
Search locations (agency token):
```
GET /locations/search?limit=100
```
Create task (per contact — GHL tasks attach to contacts):
```
POST /contacts/<contactId>/tasks
{"title": "...", "body": "...", "dueDate": "<ISO ms or ISO>", "assignedTo": "<userId>", "completed": false}
```
List contacts:
```
GET /contacts/?locationId=<loc>&query=<search>
```
Create appointment:
```
POST /calendars/appointments
{"calendarId": "...", "locationId": "...", "startTime": "ISO", "endTime": "ISO",
 "title": "...", "meetingLocationType": "custom", "meetingLocation": "...", ...}
```

## Conventions

- All times US/Eastern unless stated.
- When asked to add a task for "Tim" or "Tony" without a contact, ask whether it
  should attach to a specific customer/contact or be a general admin task.
- Verify writes by re-reading the resource after creating it.

## Messaging Channels

- Telegram home channel: `hermes send --to telegram "..."` (Get Zot DM, ID 8538206813).
- Slack workspace **Inc2biz** (bot `mrquackers`). Token scopes LACK `channels:join`/`groups:read`, so bot MUST be manually invited to a channel before it can post (API returns `not_in_channel`). Public channels: `#social` C0AHTL24U79, `#all-inc2biz` C0AJ2MSKY1Z, `#highlevel` C0AJ5N9EMRB, `#mailers` C0AJ7318QNA, `#interview-process` C0AJ73LUQDU, `#getzot` C0AJCMYGDM2, `#zotbot` C0AJN262G9F, `#agenda` C0AJN2F0E3T, `#directory-sites` C0AJN2N3H5X. Send via `hermes send --to slack:C0AJCMYGDM2 ...`.
- Email: no SMTP/IMAP credentials in `.env` yet — needs setup if email channel required.

## Scheduled Briefing

- Cron job `Daily Ops Briefing` (id **53baa6915353**) runs daily 08:00 ET (no_agent), executes `~/.hermes/scripts/ghl_daily_brief.py`, delivers to Telegram.
- Script pulls per-sub-account contact count + today's appointments from each account's calendars.
- To also deliver to Slack, must add the bot to a channel first (invite mrquackers), then set cron deliver `telegram,slack:<channel_id>`.

## Pitfalls

- `/locations/` (trailing slash, no search) returns 404 — use `/locations/search`.
- GHL v1-style endpoints may need the older `Version` header; if you get 404 on one version header, try `2021-04-15`.
- Tasks are per-contact, not per-location: always resolve a contactId first.
- The `.env` contains secrets — never print values, only key names.
