# Candidate Status Communicator — Problem Statement

## The Problem

Candidates hear nothing for weeks after applying — and it damages the employer brand. People who took time to apply deserve to know where they stand, but writing individual updates and rejections for every pipeline move always loses to something more urgent.

## The Goal

Candidates should enter the pipeline within minutes of applying, and every stage change should trigger the right communication automatically:

- **Application received** → instant acknowledgment
- **Moving forward** → next-steps email
- **Rejected** → personalized, respectful rejection (held for approval)
- **Offer extended** → congratulatory email (held for approval)

Rejections and offers **always** wait for explicit one-tap approval. A mis-dragged row must never email the wrong person.

## What Sets It Off

The agent runs autonomously on a recurring schedule, sweeping every few minutes:

- **Email intake** — Reads Gmail for application and job-board emails. Each creates the candidate's row and sends an acknowledgment in the same pass. Hand-added rows still work as a walk-in fallback.
- **Status monitoring** — Watches the status column on every candidate's row. A change drives the matching communication, picked up within minutes.

## What the Agent Does

| Trigger | Action | Requires Approval? |
|:--------|:-------|:-------------------|
| New application email detected | Extract name, email, role → create row as "applied" → send acknowledgment | ❌ No |
| Status → forward stage (interview, assessment, screening) | Send moving-forward email with next steps | ❌ No |
| Status → "rejected" | Draft personalized rejection referencing candidate's background → hold for one-tap approval | ✅ Yes |
| Status → "offer" | Draft offer/congratulations email → hold for one-tap approval | ✅ Yes |
| Every communication | Log against the candidate's row for full history | Automatic |

## AI Behavior

- **The unbreakable rule:** Rejections and offers always wait for explicit approval. Pipeline data is a suggestion for these two — never a command.
- **Honest classification:** A newsletter must never become a candidate. Doubt means park-and-ask, not send.
- **Safe by default:** Acknowledgments and stage updates run on pre-approved generic wording — a misfire embarrasses no one.
- **Human-quality rejections:** Rejections read like a human wrote them — respectful, specific, referencing the candidate's actual background. It's the last impression we leave.

## Edge Cases Handled

| Scenario | How It's Handled |
|:---------|:-----------------|
| Status set, reverted, and set again (mis-drag) | No duplicate emails; approval step catches it for rejections/offers |
| Same person applies twice | One row, one email — never a repeated communication |
| Email misclassified as application | Generic-safe wording means no harm; row is flagged |
| Candidate row with no email address | Flagged to owner — never skipped silently |
| Unrecognized status value | Owner is notified — agent never guesses the template |

## Expected Output

- Automatic acknowledgment and stage-update emails on pre-approved wording
- Personalized rejection and offer drafts, always held for one-tap approval
- A complete communication log per candidate visible in one place

## Success Criteria

No candidate ever ghosts-by-default again — everyone hears back at every stage. And in months of operation, not one rejection or offer ever goes out without explicit human approval.
