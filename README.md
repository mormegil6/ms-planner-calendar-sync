# KNIDiO Group Calendar Sync

[![Power Automate](https://img.shields.io/badge/Power%20Automate-Flow-0066FF?logo=powerautomate&logoColor=white)](https://make.powerautomate.com/)
[![Microsoft Graph](https://img.shields.io/badge/Microsoft%20Graph-API-0078D4?logo=microsoft&logoColor=white)](https://developer.microsoft.com/graph/graph-explorer)
[![Planner](https://img.shields.io/badge/Microsoft%20Planner-Tasks-31752F?logo=microsoft&logoColor=white)](https://planner.cloud.microsoft/)
[![Teams](https://img.shields.io/badge/Microsoft%20Teams-Notifications-6264A7?logo=microsoftteams&logoColor=white)](https://teams.microsoft.com/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](./LICENSE)

Power Automate solution that synchronizes Microsoft Planner tasks with an Outlook calendar and adds operational reminders for task hygiene.

## Quick Links

- [Power Automate](https://make.powerautomate.com/)
- [Power Apps Solutions](https://make.powerapps.com/)
- [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)
- [Microsoft Planner](https://planner.cloud.microsoft/)
- [Microsoft Teams](https://teams.microsoft.com/)
- [Microsoft 365 Admin Center](https://admin.microsoft.com/)

## What This Solution Includes

| Flow | Trigger | Purpose |
|---|---|---|
| **Planner ↔ Calendar Sync** | Every 15 min | Syncs Planner tasks with due dates to Outlook calendar events (create/update/delete). |
| **Calendar Cleanup** | Daily at 03:00 | Removes orphaned synced calendar events when Planner tasks no longer exist. |
| **Reset Calendar Sync** | Manual button | Deletes all synced calendar events for a clean rebuild. |
| **Task Due Date Reminder** | Daily at 09:00 | Posts Teams channel reminders for assigned tasks with no due date and completed tasks outside the Done bucket. |

## Architecture

```mermaid
flowchart LR
	A[Planner Plan] -->|List tasks| B[Planner ↔ Calendar Sync]
	B -->|Create/Update/Delete events| C[Outlook Calendar]
	D[Calendar Cleanup] -->|Find orphan events| C
	E[Reset Calendar Sync] -->|Manual reset| C
	F[Task Due Date Reminder] -->|Post reminder| G[Teams Channel]

	H[Graph Explorer] -. used for IDs .-> A
	H -. used for IDs .-> C
```

## Placeholders You Must Replace

Update values in `Workflows/*.json` before import:

| Placeholder | Meaning |
|---|---|
| `YOUR_GROUP_OR_TEAM_ID` | Microsoft 365 Group / Teams Team GUID |
| `YOUR_PLAN_ID` | Planner plan ID |
| `YOUR_CALENDAR_ID` | Outlook calendar ID (Exchange/Graph identifier) |
| `YOUR_CHANNEL_ID` | Teams channel ID for reminder messages |
| `YOUR_DONE_BUCKET_ID` | Planner bucket ID for `✅ Zrobione` |
| `YOUR_RESERVE_BUCKET_ID` | Planner bucket ID for `💡 Rezerwa/zaległe` |

## Deployment Guide

1. Open the solution folder and replace all placeholder values listed above.
2. Create a ZIP package while keeping the current directory structure.
3. Go to [Power Apps Solutions](https://make.powerapps.com/) and import the ZIP.
4. During import, map connection references:
- Office 365 Outlook
- Planner
- Teams
5. Enable all imported flows in [Power Automate](https://make.powerautomate.com/).
6. Run a manual smoke test:
- Trigger **Reset Calendar Sync** once (optional, clean start).
- Wait for **Planner ↔ Calendar Sync** to run.
- Verify events in Outlook calendar and reminders in Teams channel.

## Where To Get Required IDs

- **Group/Team ID**:
Use Teams "Get link to team" and copy `groupId` from the URL.
- **Plan ID**:
Open the plan in [Planner](https://planner.cloud.microsoft/) and copy the `planId` parameter from the URL.
- **Channel ID**:
Use Teams "Get link to channel" and copy the channel thread ID.
- **Calendar ID**:
Use [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) with:
`GET https://graph.microsoft.com/v1.0/me/calendars`
- **Bucket IDs**:
Use Graph Explorer with:
`GET https://graph.microsoft.com/v1.0/planner/plans/{planId}/buckets`

## Useful Graph Endpoints

- List calendars:
`GET /me/calendars`
- List Planner plans for a group:
`GET /groups/{groupId}/planner/plans`
- List tasks in a plan:
`GET /planner/plans/{planId}/tasks`
- List buckets in a plan:
`GET /planner/plans/{planId}/buckets`

## Security Note

This repository intentionally uses `YOUR_*` placeholders instead of tenant-specific IDs.
Keep real tenant identifiers in private configuration or local-only working copies.

## Troubleshooting

| Problem | Likely Cause | Fix |
|---|---|---|
| Import fails in Power Apps | Invalid ZIP structure | Rebuild ZIP with original root layout (`Workflows/`, `solution.xml`, etc.). |
| Flow import asks for missing connections | Connection references not mapped | Map Outlook, Planner, and Teams connections during import. |
| Sync flow runs but no events are created | Missing due dates or wrong IDs | Verify `YOUR_PLAN_ID`, `YOUR_CALENDAR_ID`, and that tasks have `dueDateTime`. |
| Cleanup deletes too much or too little | Wrong calendar ID or stale placeholders | Re-check placeholder replacement and confirm target calendar with Graph `/me/calendars`. |
| Reminder flow does not post to Teams | Wrong team/channel ID or permissions | Validate `YOUR_GROUP_OR_TEAM_ID` and `YOUR_CHANNEL_ID`; test connector access in Power Automate. |
| Push to GitLab is rejected | Remote changed since last push | Run `git pull origin main --rebase`, resolve conflicts, then push again. |

## Handy Validation Checklist

- Confirm no `YOUR_*` placeholders remain before production import.
- Verify all four flows are turned on after import.
- Create a test Planner task with a due date and confirm calendar sync.
- Mark a task complete outside Done bucket and confirm Teams reminder.

## License

MIT
