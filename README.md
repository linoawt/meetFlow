# meetFlow

> Intelligent event scheduling and conflict automation powered by Google APIs and Twilio.

---

## 1. Overview & Architecture

meetFlow is an event-driven automation platform that detects incoming calendar invitations in Gmail, validates availability against Google Calendar, auto-books free slots, and sends instant WhatsApp alerts when conflicts arise.

```text
Gmail Event
    │
    ▼
Google Cloud Pub/Sub Webhook
    │
    ▼
meetFlow Backend (Parser)
    │
    ▼
Google Calendar FreeBusy Check
    ├── [FREE]  ──► Create Calendar Event ──┐
    └── [BUSY]  ──► Twilio WhatsApp Alert  ──┤
                                            ▼
                                   Activity Log & Dashboard


2. Core Features

Gmail & Pub/Sub Monitoring: Real-time push notifications via Google Cloud Pub/Sub webhook (/api/pubsub/webhook).

Invitation Extraction: Extracts event title, start time, end time, participants, and context from message payloads.

Calendar Availability Check: Queries Google Calendar freebusy for target time windows.

Auto-Scheduling: Inserts confirmed events directly to the user's primary calendar if unblocked.

Conflict Notifications: Dispatches WhatsApp alerts via Twilio REST API with meeting details and conflict warnings.

Activity Logging & Dashboard: Persists all incoming invitations, conflict statuses, and delivery confirmations.

3. Repository Structure

meetFlow/
├── public/                 # Frontend client
│   ├── index.html          # Dashboard interface
│   ├── style.css           # Styling
│   └── app.js              # Client state, auth, and metrics
├── services/               # External integration clients
│   ├── gmail.js            # Gmail API fetch & watch management
│   ├── calendar.js         # FreeBusy check & event insertion
│   ├── pubsub.js           # Push notification handler & verification
│   └── twilio.js           # WhatsApp messaging client
├── utils/
│   ├── parser.js           # Invitation parsing engine
│   └── logger.js           # Formatted event & error logging
├── .env.example            # Environment variable template
├── package.json
└── server.js               # Express application entry point

4. Environment Configuration

Store secrets exclusively in .env (never commit this file to version control):

# Application
PORT=3000
NODE_ENV=development
SESSION_SECRET=<YOUR_RANDOM_SESSION_SECRET>

# Google OAuth & Cloud
GOOGLE_CLIENT_ID=<YOUR_GOOGLE_CLIENT_ID>
GOOGLE_CLIENT_SECRET=<YOUR_GOOGLE_CLIENT_SECRET>
GOOGLE_REDIRECT_URI=http://localhost:3000/oauth2callback
GOOGLE_PROJECT_ID=<YOUR_GOOGLE_PROJECT_ID>

# Google Pub/Sub
PUBSUB_TOPIC=projects/<YOUR_GOOGLE_PROJECT_ID>/topics/<YOUR_TOPIC_NAME>
PUBSUB_VERIFICATION_TOKEN=<YOUR_PUBSUB_VERIFICATION_TOKEN>

# Twilio WhatsApp
TWILIO_ACCOUNT_SID=<YOUR_TWILIO_ACCOUNT_SID>
TWILIO_AUTH_TOKEN=<YOUR_TWILIO_AUTH_TOKEN>
TWILIO_WHATSAPP_FROM=whatsapp:+14155238886
TWILIO_WHATSAPP_TO=whatsapp:<YOUR_E164_PHONE_NUMBER>

5. Decision & Execution Engine

Incoming Invitation
       │
       ▼
Extract: Title, Start, End
       │
       ▼
Query Google Calendar FreeBusy
       │
       ├── Calendar Free ──► Insert event (Status: SCHEDULED)
       │
       └── Calendar Busy ──► Format conflict alert
                             Send Twilio WhatsApp (Status: CONFLICT)
       │
       ▼
Write to Event Log -> Broadcast to Dashboard

6. API Surface

Method	Endpoint	Description
GET	/	Serves dashboard frontend
GET	/auth/google	Initiates Google OAuth 2.0 flow
GET	/oauth2callback	Handles OAuth token exchange
POST	/api/pubsub/webhook	Receives Google Cloud Pub/Sub push messages
GET	/api/events	Fetches parsed event log history
GET	/api/status	Returns integration health (Gmail, Calendar, Twilio)


7. Setup & Execution

Prerequisites

Node.js 18+ and npm.

Google Cloud project with Gmail API, Calendar API, and Pub/Sub enabled.

Twilio account with WhatsApp Sandbox or Business profile.


Quickstart

# 1. Install dependencies
npm install

# 2. Configure environment
cp .env.example .env
# Fill in credentials in .env

# 3. Start server
npm start


8. Testing Scenarios

Available Slot: Send email invitation for a clear time window 
→
→ verify event creation in Google Calendar and SCHEDULED status in dashboard.

Conflict Scenario: Send email invitation conflicting with an existing event 
→
→ verify WhatsApp alert received with details and CONFLICT status logged.



Author

Success Azibapu (Linostudiong)