# KNIDiO Group Calendar Sync

Power Automate solution that synchronizes Microsoft Planner tasks with an Outlook group calendar.

## Flows

| Flow | Trigger | Description |
|---|---|---|
| **Planner ↔ Calendar Sync** | Every 15 min | Syncs Planner tasks (with due dates) to Outlook calendar events. Creates, updates, or deletes events as needed. |
| **Calendar Cleanup** | Daily at 3:00 AM | Removes orphaned calendar events for tasks that no longer exist in Planner. |
| **Reset Calendar Sync** | Manual (button) | Deletes all synced calendar events — useful for a clean re-sync. |
| **Task Due Date Reminder** | Daily at 9:00 AM | Posts a Teams channel reminder for: (1) assigned tasks missing a due date, (2) completed tasks not moved to the "Done" bucket. |

## Setup

This is a Dataverse solution package. To deploy:

1. Replace placeholder values in all `Workflows/*.json` files:

   | Placeholder | Description |
   |---|---|
   | `YOUR_GROUP_OR_TEAM_ID` | Microsoft 365 Group / Teams team ID (GUID) |
   | `YOUR_PLAN_ID` | Planner plan ID |
   | `YOUR_CALENDAR_ID` | Outlook calendar ID (base64 Exchange identifier) |
   | `YOUR_CHANNEL_ID` | Teams channel ID (used by the Reminder flow) |
   | `YOUR_DONE_BUCKET_ID` | Planner bucket ID for "✅ Zrobione" |
   | `YOUR_RESERVE_BUCKET_ID` | Planner bucket ID for "💡 Rezerwa/zaległe" |

2. ZIP all files (maintaining directory structure) into a `.zip` archive.
3. Import in [Power Apps](https://make.powerapps.com/) → Solutions → Import solution.
4. Configure connection references (Office 365 Outlook, Planner, Teams) during import.
5. Turn on the flows.

## Finding your IDs

- **Group/Team ID**: In Teams, right-click the team → "Get link to team" → extract the `groupId` parameter.
- **Plan ID**: Open the plan in Planner → copy from the URL (`planId` query parameter).
- **Calendar ID**: Use Graph Explorer (`GET /me/calendars`) to list calendars and find the `id`.
- **Channel ID**: In Teams, right-click a channel → "Get link to channel" → extract the channel identifier.
- **Bucket IDs**: Use Graph Explorer (`GET /planner/plans/{planId}/buckets`) to list buckets.

## License

MIT
