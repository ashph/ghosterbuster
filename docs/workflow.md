# GhosterBuster workflow notes

## Verified implementation

The Make scenario contains two connected modules:

1. **Google Sheets — advanced row search.** Reads the Master Tracker and applies the query below.
2. **Gmail — send an email.** Sends an HTML internal alert for each returned candidate record.

```sql
select * where I >= 3 and J = 'Active'
```

Column I contains days stale. Column J contains status. The query includes records marked `Active` when the stale value is at least three days. Other statuses do not match this query.

The scenario runs on a configured schedule. The interval and time window are deployment settings, rather than a guarantee that this repository provides a running service.

## Fields used in the alert

| Column | Field | Use |
|---|---|---|
| A | Recruiter | Greeting / ownership context |
| B | Candidate name | Subject, candidate summary, message greeting |
| C | Candidate email | Recipient of the prefilled candidate message |
| D | Role | Candidate summary and message body |
| E | Stage | Candidate summary |
| H | Last stage change / activity | Date displayed in the alert |
| I | Days stale | Eligibility rule and alert badge |
| J | Status | Eligibility rule |

## Review and send

The internal alert is sent automatically to one configured internal inbox. It offers three `mailto:` links, which populate a subject, candidate recipient, and message body in the recruiter's email client.

Selecting a link opens a message for review; it does not send the candidate email through Make. The recruiter checks that the candidate is still in consideration and that any promised timing is accurate, edits the message as needed, and sends it.

After following up, the recruiter updates the tracker. The Make scenario has no write-back module, sent-email check, or separate notification log. Records that still match the query can appear again on later runs.

## Boundaries

- The Google Sheets manager dashboard displays summary metrics, stage breakdowns, and recruiter-level pipeline health. Its formulas and leaderboard scoring are not part of the two-module Make scenario or this repository.
- This implementation uses predefined message templates, rather than AI-generated messages.
- Days stale comes from the sheet. Its calculation is not implemented in these two Make modules.
- The alert recipient is configured centrally; it is not dynamically routed using the recruiter's name.
- Candidate communication and tracker maintenance remain recruiter actions.
- Screenshots demonstrate the interface; they are not a single synchronized run log.

## Potential extensions

These are future implementation options, not capabilities of the verified two-module scenario:

- Map recruiter ownership to the appropriate internal email address.
- Track the last candidate contact separately from the last stage change.
- Record the last alert date to control repeated reminders.
- Record confirmed follow-up activity and update the tracker automatically.

## Reusing the approach

To implement a similar workflow, create a tracker with the fields above, define the stale-day calculation, connect your own Google Sheets and Gmail accounts in Make, configure an internal recipient, and build the two modules. Test with synthetic candidate records before using operational recruiting data.

This repository documents the approach; it does not include credentials, connection identifiers, live tracker links, or a Make blueprint.
