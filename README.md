# Candidate Status Communicator

An AI-powered recruitment automation agent built on [Activepieces](https://www.activepieces.com/) that monitors Gmail for applications, classifies emails via AI, tracks candidates in Google Sheets, detects status changes, and sends personalized emails with human approval gates.

>  **See [PROBLEM_STATEMENT.md](./PROBLEM_STATEMENT.md) for the full problem definition and what this agent solves.**

---

##  Quick Start — Use the Template

The fastest way to get started:

** [Use the Activepieces Template](https://cloud.activepieces.com/templates/DNIbhrIvC2gqqIe45duB2)**

Click the link above to import the flow directly into your Activepieces workspace. You can modify the template according to your needs.

> If you prefer manual import, follow the JSON import steps below.

---

##  Prerequisites

Before setting up the agent, make sure you have:

- [ ] An [Activepieces](https://cloud.activepieces.com/) account (free tier works)
- [ ] A **Gmail account** (the agent reads applications from and sends emails through this account)
- [ ] A **Google account** with Google Sheets access (can be the same Gmail account)
- [ ] Activepieces AI credits or a connected AI provider (for email classification and draft generation)

---

##  Setup Guide

### Step 1: Import the Flow

**Option A — Template (Recommended)**

1. Open the [template link](https://cloud.activepieces.com/templates/DNIbhrIvC2gqqIe45duB2)
2. Click **"Use Template"**
3. It will be added to your Activepieces workspace automatically

**Option B — JSON Import**

1. Download the `Candidate Status Communicator - v2.json` file from this repo
2. In Activepieces, go to **Flows** → **Import Flow**
3. Upload the JSON file
4. The flow will appear in your dashboard

---

### Step 2: Connect Gmail (OAuth)

The agent uses Gmail for two purposes:
- **Reading** incoming application emails
- **Sending** acknowledgments, status updates, rejections, and offers

**To connect:**

1. Open the imported flow in the Activepieces editor
2. Click on any Gmail step (e.g., the "Find Emails" trigger or any "Send Email" step)
3. In the connection dropdown, click **"+ New Connection"**
4. Select **"Gmail"** → click **"Connect"**
5. A Google sign-in popup will appear — sign in with the Gmail account you want the agent to use
6. Grant the requested permissions:
   - `Read, compose, and send emails`
   - `View your email messages and settings`
7. Click **"Allow"**
8. Name the connection (e.g., `My Gmail`) and save

>  **Important:** Use the same Gmail connection across ALL Gmail steps in the flow. After connecting once, select the same connection from the dropdown in every Gmail step.

**Gmail steps that need this connection:**

| Step | Purpose |
|:-----|:--------|
| Find Emails | Reads incoming application emails |
| Send Email (acknowledgment) | Sends application received confirmation |
| Send Email (moving forward) | Sends stage-update notifications |
| Request Approval in Email (rejection) | Sends rejection draft for your approval |
| Send Email (rejection) | Sends the approved rejection |
| Request Approval in Email (offer) | Sends offer draft for your approval |
| Send Email (offer) | Sends the approved offer |
| Send Email (uncertain) | Forwards uncertain emails to you |
| Send Email (unknown status) | Alerts you about unrecognized statuses |

---

### Step 3: Connect Google Sheets (OAuth)

The agent uses Google Sheets to create and manage the candidate tracking spreadsheet.

**To connect:**

1. Click on any Google Sheets step (e.g., "Create Spreadsheet" or "Get All Rows")
2. In the connection dropdown, click **"+ New Connection"**
3. Select **"Google Sheets"** → click **"Connect"**
4. Sign in with your Google account
5. Grant permissions:
   - `See, edit, create, and delete all your Google Sheets spreadsheets`
6. Click **"Allow"**
7. Name the connection (e.g., `My Google Sheets`) and save

>  **Important:** Use the same Google Sheets connection across ALL Sheets steps. The agent creates a spreadsheet on first run and references it throughout.

**Google Sheets steps that need this connection:**

| Step | Purpose |
|:-----|:--------|
| Create Spreadsheet | Creates the candidate tracker on first run |
| Insert Row (headers) | Adds column headers |
| Insert Row (example) | Adds an example row |
| Get All Rows | Reads all candidates for status monitoring |
| Find Rows | Checks if a candidate already exists |
| Insert Row (new candidate) | Adds new candidates from emails |
| Update Row (various) | Logs communications against candidate rows |

---

### Step 4: Answer Builder Questions

When you publish the flow, Activepieces will prompt you with **3 builder questions**. These configure the agent for your company:

| Question | What to Enter | Example |
|:---------|:--------------|:--------|
| **Owner Email** | Your email address — where approval requests and alerts are sent | `you@company.com` |
| **Company Name** | Your company/org name — used in all candidate-facing emails | `Acme Corp` |
| **AI Model** | The AI model for email classification and drafting (leave default if unsure) | `anthropic/claude-haiku-4.5` |

---

### Step 5: Publish and Test

1. Click **"Publish"** in the top-right corner of the flow editor
2. The agent will start running automatically on its schedule (every few minutes)

**To test the full pipeline:**

1. **Send a test application email** to the connected Gmail account with a subject like: *"Application for Software Engineer — John Doe"*
2. **Wait for the next sweep** (1–5 minutes depending on your schedule trigger setting)
3. **Check your Google Sheets** — a new spreadsheet called `Candidate Tracker` should appear with the candidate's row
4. **Check the candidate's inbox** — they should receive an acknowledgment email
5. **Change the status** in the sheet from `applied` to `interview` — the candidate gets a "moving forward" email
6. **Change the status** to `rejected` — you receive an approval email with the AI-drafted rejection. Click **Approve** to send it or **Reject** to cancel

---

##  Google Sheet Structure

The agent creates a spreadsheet with these columns:

| Column | Header | Purpose |
|:-------|:-------|:--------|
| A | **Name** | Candidate's full name |
| B | **email** | Candidate's email address |
| C | **role** | Position they applied for |
| D | **Status** | Current pipeline status |
| E | **applied date** | Date the application was received |
| F | **Last communication** | Most recent action taken |
| G | **Communication log** | Full history of all communications |
| H | **Notes** | Background summary / additional notes |

### Supported Status Values

| Status | Behavior |
|:-------|:---------|
| `applied` | Default for new candidates — no action taken |
| `interview` | Sends moving-forward email automatically |
| `assessment` | Sends moving-forward email automatically |
| `screening` | Sends moving-forward email automatically |
| `assignment` | Sends moving-forward email automatically |
| `rejected` | AI drafts personalized rejection → held for your approval |
| `offer` / `accepted` | AI drafts offer email → held for your approval |
| Any other value | You receive an "unknown status" alert email |

---

### Two-Phase Pipeline Summary

| Phase | What It Does | Key Steps |
|:------|:-------------|:----------|
| **Phase 1 — Email Intake** | Reads Gmail for new application emails, classifies them with AI, extracts candidate data, creates sheet rows, and sends acknowledgments | Classify → Extract → Deduplicate → Insert → Ack |
| **Phase 2 — Status Monitor** | Reads all sheet rows, compares against a stored snapshot to detect status changes, and routes each change to the appropriate communication action | Snapshot compare → Route by status → Send/Draft → Approve → Log |

> **State Management:** The agent uses Activepieces Storage (key-value store) to persist the sheet ID, last processed email ID, and a JSON snapshot of all candidate statuses between runs. This prevents duplicate processing and enables change detection.

---

##  Customization Guide

You can modify the template to fit your needs:

### Change Email Templates
- Open the Gmail "Send Email" steps and edit the `body` field
- For rejection/offer emails, modify the AI prompt in the "Ask AI" steps to match your tone and style

### Add More Status Values
- Open the status Router step
- Add a new branch with your custom status (e.g., `final round`)
- Add the corresponding email action

### Change the Schedule
- Click the trigger step (Schedule)
- Adjust the interval (e.g., every 1 minute, every 10 minutes)

### Add More Columns to the Sheet
- Update the "Create Spreadsheet" step to add headers
- Update all "Insert Row" and "Update Row" steps to include the new column

---

##  Tech Stack

| Component | Technology |
|:----------|:-----------|
| **Automation Platform** | [Activepieces](https://www.activepieces.com/) |
| **Email** | Gmail API (OAuth 2.0) |
| **Database** | Google Sheets API (OAuth 2.0) |
| **AI / LLM** | Claude AI (via Activepieces Universal AI) |
| **State Management** | Activepieces Storage (key-value) |
| **Approval System** | Gmail Request Approval in Email |

---

##  Troubleshooting

| Issue | Solution |
|:------|:---------|
| Agent not picking up emails | Check Gmail connection is valid. Verify the schedule trigger is active and the flow is published |
| Duplicate candidates appearing | Ensure the "Find Rows" step is checking column B (email) correctly |
| Approval emails not arriving | Check your spam folder. Verify the owner email in builder questions is correct |
| Status changes not detected | The agent uses a snapshot system — it only detects changes after the first run establishes a baseline |
| Google Sheets "permission denied" | Re-authenticate the Google Sheets connection. Make sure the connected account owns the spreadsheet |
| AI steps failing | Check your Activepieces AI credit balance or reconnect your AI provider |

---

##  License

This project is open source. Feel free to use, modify, and distribute.

---
