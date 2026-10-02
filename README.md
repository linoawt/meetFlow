# meetFlow

> Intelligent event scheduling and workflow automation powered by Google APIs and Twilio.

## Overview

meetFlow is a full-stack event scheduling and workflow automation platform designed to automatically process event invitations, check calendar availability, schedule conflict-free events, and notify users when scheduling conflicts occur.

The platform integrates:

* Gmail
* Google Calendar
* Google Cloud Pub/Sub
* Google OAuth 2.0
* Twilio
* WhatsApp

The core workflow is:

```text
Gmail Invitation
       |
       v
Google Pub/Sub
       |
       v
meetFlow Backend
       |
       v
Invitation Parser
       |
       v
Calendar FreeBusy Check
       |
       +----------------------+
       |                      |
       v                      v
     FREE                    BUSY
       |                      |
       v                      v
Create Calendar Event    Send WhatsApp Alert
       |                      |
       +----------+-----------+
                  |
                  v
           Activity Logging
                  |
                  v
          Dashboard Update
```

---

# Project Goals

meetFlow is designed to solve the repetitive parts of event scheduling by connecting email, calendar, notifications, and automation into a single workflow.

The primary goals are:

1. Automatically detect event invitations.
2. Extract event information from incoming messages.
3. Check Google Calendar availability.
4. Automatically create events when the requested time is available.
5. Detect scheduling conflicts.
6. Send WhatsApp notifications when conflicts occur.
7. Maintain a record of automated actions.
8. Provide a dashboard for monitoring events and workflow activity.
9. Provide a foundation for AI-assisted workflow automation.
10. Provide a modular architecture that can scale as the application grows.

---

# Core Features

## Gmail Integration

meetFlow monitors Gmail for incoming event-related messages.

The system can process invitation information such as:

* Event title
* Start time
* End time
* Organizer
* Participants
* Invitation content

---

## Google Cloud Pub/Sub

Google Cloud Pub/Sub provides an event-driven mechanism for notifying the backend when Gmail activity occurs.

```text
Gmail
  |
  v
Gmail Watch
  |
  v
Google Cloud Pub/Sub
  |
  v
meetFlow Webhook
```

This allows the application to react to incoming messages instead of continuously polling Gmail.

---

## Google Calendar Automation

The system checks the user's primary calendar using the Google Calendar FreeBusy API.

If the requested time is available, meetFlow can create the event automatically.

If the requested time is occupied, the event is treated as a conflict.

---

## Twilio WhatsApp Alerts

When a scheduling conflict is detected, meetFlow can send a WhatsApp notification through Twilio.

Example workflow:

```text
Calendar Conflict
       |
       v
Conflict Detected
       |
       v
Twilio API
       |
       v
WhatsApp Message
```

---

## Scheduling Dashboard

The dashboard provides a centralized interface for monitoring:

* Upcoming events
* Incoming invitations
* Scheduling activity
* Calendar conflicts
* Integration status
* Notifications
* Workflow logs

---

## Authentication

Google OAuth 2.0 is used to authenticate users and authorize access to Google services.

---

# System Architecture

meetFlow is organized into several architectural layers.

```text
+------------------------------------------------------------------+
|                         meetFlow Platform                        |
+------------------------------------------------------------------+
|                                                                  |
|  PRESENTATION LAYER                                              |
|  +------------------------------------------------------------+  |
|  |                  Scheduling Dashboard                      |  |
|  |                                                            |  |
|  | Events | Invitations | Activity | Integrations | Settings |  |
|  +------------------------------------------------------------+  |
|                              |                                   |
|                              v                                   |
|  APPLICATION LAYER                                               |
|  +------------------------------------------------------------+  |
|  |                     Express.js API                         |  |
|  |                                                            |  |
|  | Authentication | Events | Scheduling | Status | Webhooks   |  |
|  +------------------------------------------------------------+  |
|                              |                                   |
|                              v                                   |
|  WORKFLOW LAYER                                                  |
|  +------------------------------------------------------------+  |
|  |                   Scheduling Engine                        |  |
|  |                                                            |  |
|  | Invitation Parser | Conflict Detection | Event Creation   |  |
|  | Notification Manager | Workflow Logger                    |  |
|  +------------------------------------------------------------+  |
|                              |                                   |
|                              v                                   |
|  INTEGRATION LAYER                                               |
|  +------------------------------------------------------------+  |
|  | Gmail | Calendar | Pub/Sub | Twilio | WhatsApp             |  |
|  +------------------------------------------------------------+  |
|                              |                                   |
|                              v                                   |
|  EXTERNAL SERVICES                                                |
|  +------------------------------------------------------------+  |
|  | Google Cloud | Gmail | Google Calendar | Twilio            |  |
|  +------------------------------------------------------------+  |
|                                                                  |
+------------------------------------------------------------------+
```

---

# Architectural Workflow

The complete meetFlow workflow begins when the user connects their Google account.

```text
                         USER
                           |
                           v
                  +----------------+
                  | Google OAuth 2  |
                  +-------+--------+
                          |
                          v
                  +----------------+
                  | Authentication |
                  | & Permissions  |
                  +-------+--------+
                          |
                          v
             +---------------------------+
             |      meetFlow Backend     |
             |       Express.js          |
             +-------------+-------------+
                           |
             +-------------+-------------+
             |                           |
             v                           v
      +-------------+             +-------------+
      | Gmail Watch |             |   Calendar  |
      +------+------+             +-------------+
             |
             v
      +-------------+
      | Google      |
      | Pub/Sub     |
      +------+------+
             |
             v
      +-------------+
      | Webhook     |
      | Receiver    |
      +------+------+
             |
             v
      +-------------+
      | Invitation  |
      | Parser      |
      +------+------+
             |
             v
      +----------------------+
      | Calendar FreeBusy    |
      | Conflict Check       |
      +----------+-----------+
                 |
          +------+------+
          |             |
          v             v
        FREE           BUSY
          |             |
          v             v
 +----------------+  +----------------+
 | Create Event   |  | Twilio WhatsApp|
 | in Calendar    |  | Alert          |
 +-------+--------+  +-------+--------+
         |                   |
         +---------+---------+
                   |
                   v
          +-------------------+
          | Workflow Logging  |
          +---------+---------+
                    |
                    v
          +-------------------+
          | Dashboard Update  |
          +-------------------+
```

---

# Event Processing Workflow

```text
1. Gmail receives invitation
              |
              v
2. Gmail detects mailbox change
              |
              v
3. Gmail sends notification to Pub/Sub
              |
              v
4. Pub/Sub calls meetFlow webhook
              |
              v
5. Backend retrieves Gmail message
              |
              v
6. Invitation parser extracts:
       - Event title
       - Start time
       - End time
       - Organizer
       - Participants
              |
              v
7. Scheduling engine validates event
              |
              v
8. Google Calendar FreeBusy API
              |
       +------+------+
       |             |
       v             v
     FREE           BUSY
       |             |
       v             v
Create event     Send WhatsApp
       |             |
       v             v
Success log      Conflict log
       |             |
       +------+------+
              |
              v
       Dashboard update
```

---

# Repository Architecture

The repository is organized to separate the frontend, backend services, workflow logic, utility functions, configuration, and documentation.

```text
meetFlow/
|
├── public/
|   ├── index.html
|   ├── style.css
|   └── app.js
|
├── services/
|   ├── gmail.js
|   ├── calendar.js
|   ├── pubsub.js
|   └── twilio.js
|
├── utils/
|   ├── parser.js
|   └── logger.js
|
├── server.js
|
├── package.json
├── package-lock.json
├── .env
├── .env.example
├── .gitignore
└── README.md
```

---

# Repository File Architecture

## `public/`

The `public` directory contains the frontend of the application.

```text
public/
|
├── index.html
├── style.css
└── app.js
```

### `public/index.html`

Responsible for the structure of the dashboard interface.

It contains:

* Application layout
* Navigation
* Dashboard sections
* Event tables
* Statistics cards
* Activity areas
* Integration status
* User interface components

The HTML should focus on structure rather than business logic.

---

### `public/style.css`

Responsible for the visual presentation of the application.

It handles:

* Layout
* Typography
* Colors
* Spacing
* Cards
* Tables
* Navigation
* Buttons
* Status indicators
* Responsive behavior
* Mobile layouts

---

### `public/app.js`

Responsible for frontend behavior.

It handles communication between the dashboard and the backend API.

Example responsibilities:

```text
Fetch events
Fetch dashboard statistics
Fetch integration status
Display activity logs
Update UI
Handle navigation
Submit scheduling requests
Handle API responses
Handle frontend errors
```

The frontend should communicate with the backend through API endpoints rather than directly accessing Google or Twilio services.

---

# `services/`

The `services` directory contains integrations with external platforms.

```text
services/
|
├── gmail.js
├── calendar.js
├── pubsub.js
└── twilio.js
```

This separation prevents `server.js` from becoming a large monolithic file.

---

## `services/gmail.js`

Responsible for Gmail API operations.

Possible responsibilities:

```text
Authenticate Gmail
Retrieve messages
Retrieve message details
Create Gmail watch
Process Gmail notifications
Retrieve message history
```

---

## `services/calendar.js`

Responsible for Google Calendar operations.

Possible responsibilities:

```text
Check calendar availability
Run FreeBusy queries
Create events
Retrieve events
Update events
Delete events
Detect scheduling conflicts
```

Example FreeBusy request:

```javascript
const response = await calendar.freebusy.query({
  requestBody: {
    timeMin: startTime,
    timeMax: endTime,
    items: [
      {
        id: "primary"
      }
    ]
  }
});
```

---

## `services/pubsub.js`

Responsible for Google Cloud Pub/Sub functionality.

Possible responsibilities:

```text
Receive Pub/Sub notifications
Validate webhook requests
Decode Pub/Sub messages
Process Gmail history IDs
Trigger invitation processing
```

---

## `services/twilio.js`

Responsible for Twilio communication.

Possible responsibilities:

```text
Initialize Twilio client
Send WhatsApp messages
Format notifications
Handle delivery errors
Record notification status
```

---

# `utils/`

The `utils` directory contains reusable application utilities.

```text
utils/
|
├── parser.js
└── logger.js
```

---

## `utils/parser.js`

Responsible for extracting useful event information from invitation messages.

Possible output:

```javascript
{
  title: "Project Meeting",
  startTime: "2026-10-10T10:00:00",
  endTime: "2026-10-10T11:00:00",
  organizer: "example@example.com"
}
```

The parser can later be extended with AI capabilities for handling less structured invitation messages.

---

## `utils/logger.js`

Responsible for application and workflow logging.

Possible log categories:

```text
INFO
SUCCESS
WARNING
CONFLICT
ERROR
```

Example:

```text
[INFO] Gmail invitation received
[SUCCESS] Invitation parsed
[INFO] Checking calendar availability
[SUCCESS] Calendar slot available
[SUCCESS] Event created
```

---

# `server.js`

`server.js` is the primary backend entry point.

It initializes:

* Express
* Environment configuration
* Authentication
* API routes
* Webhook routes
* Application middleware
* Workflow coordination

The long-term goal should be to keep `server.js` responsible for application orchestration rather than placing every business operation inside it.

```text
server.js
    |
    +-- Authentication
    |
    +-- API Routes
    |
    +-- Webhooks
    |
    +-- Workflow Coordination
    |
    +-- Services
          |
          +-- Gmail
          +-- Calendar
          +-- Pub/Sub
          +-- Twilio
```

---

# Configuration Files

## `package.json`

Defines project metadata, dependencies, and scripts.

Example dependencies:

```json
{
  "dependencies": {
    "dotenv": "^17.0.0",
    "express": "^5.1.0",
    "googleapis": "^160.0.0",
    "twilio": "^5.10.0"
  }
}
```

---

## `package-lock.json`

Contains the exact dependency tree generated by npm.

It should be committed to Git.

---

## `.env`

Contains local environment variables and secrets.

Example:

```env
PORT=3000

GOOGLE_CLIENT_ID=your_google_client_id
GOOGLE_CLIENT_SECRET=your_google_client_secret
GOOGLE_REDIRECT_URI=http://localhost:3000/oauth2callback

GOOGLE_PROJECT_ID=your_google_cloud_project_id
PUBSUB_TOPIC=your_pubsub_topic
PUBSUB_VERIFICATION_TOKEN=your_pubsub_verification_token

TWILIO_ACCOUNT_SID=your_twilio_account_sid
TWILIO_AUTH_TOKEN=your_twilio_auth_token
TWILIO_WHATSAPP_FROM=whatsapp:+14155238886
TWILIO_WHATSAPP_TO=whatsapp:+234XXXXXXXXXX

SESSION_SECRET=replace_with_a_secure_random_value
```

The `.env` file must never be committed to Git.

---

## `.env.example`

Contains the structure of required environment variables without exposing real credentials.

```env
PORT=3000

GOOGLE_CLIENT_ID=
GOOGLE_CLIENT_SECRET=
GOOGLE_REDIRECT_URI=

GOOGLE_PROJECT_ID=
PUBSUB_TOPIC=
PUBSUB_VERIFICATION_TOKEN=

TWILIO_ACCOUNT_SID=
TWILIO_AUTH_TOKEN=
TWILIO_WHATSAPP_FROM=
TWILIO_WHATSAPP_TO=

SESSION_SECRET=
```

Developers can copy this file to create their local `.env` file.

---

## `.gitignore`

```gitignore
.env
node_modules/
```

Additional generated files and sensitive credentials should also be excluded as the project grows.

---

# Website Design Architecture

The meetFlow frontend is designed as a centralized SaaS-style scheduling dashboard.

The interface should prioritize:

* Event visibility
* Scheduling status
* Connection status
* Workflow activity
* Calendar conflicts
* Integration management
* Responsive design
* Minimal navigation

---

# Main Website Structure

```text
meetFlow
|
+-- Dashboard
|     |
|     +-- Overview
|     +-- Today's Events
|     +-- Upcoming Events
|     +-- Scheduling Activity
|     +-- Connection Status
|
+-- Events
|     |
|     +-- All Events
|     +-- Upcoming
|     +-- Completed
|     +-- Conflicts
|
+-- Invitations
|     |
|     +-- Incoming Invitations
|     +-- Processed
|     +-- Pending
|     +-- Failed
|
+-- Activity
|     |
|     +-- Scheduling Logs
|     +-- Notifications
|     +-- Integration Events
|
+-- Integrations
|     |
|     +-- Google Account
|     +-- Gmail
|     +-- Google Calendar
|     +-- Twilio
|     +-- WhatsApp
|
+-- Settings
|     |
|     +-- Account
|     +-- Notifications
|     +-- Scheduling Rules
|     +-- Integrations
|     +-- Security
|
+-- Authentication
      |
      +-- Google Login
      +-- OAuth Callback
```

---

# Dashboard Design

The dashboard is the primary interface of meetFlow.

```text
+---------------------------------------------------------------------+
| meetFlow                                      User        Settings  |
+---------------------------------------------------------------------+
|                                                                     |
| Sidebar              |  Dashboard                                   |
|                      |                                               |
| Dashboard            |  Good morning, User                          |
| Events               |  Here's your scheduling overview.             |
| Invitations          |                                               |
| Activity             |  +------------+ +------------+ +-----------+ |
| Integrations         |  | Events     | | Invitations| | Conflicts | |
| Settings             |  |     8     | |     3      | |     2     | |
|                      |  +------------+ +------------+ +-----------+ |
|                      |                                               |
|                      |  Today's Schedule                             |
|                      |  +-----------------------------------------+ |
|                      |  | 09:00  Team Meeting                      | |
|                      |  | 11:00  Client Call                       | |
|                      |  | 14:00  Project Review                    | |
|                      |  +-----------------------------------------+ |
|                      |                                               |
|                      |  Recent Activity                              |
|                      |  +-----------------------------------------+ |
|                      |  | Event automatically scheduled            | |
|                      |  | Calendar conflict detected               | |
|                      |  | WhatsApp alert sent                      | |
|                      |  +-----------------------------------------+ |
|                                                                     |
+---------------------------------------------------------------------+
```

---

# Dashboard Components

## Navigation Sidebar

```text
Dashboard
Events
Invitations
Activity
Integrations
Settings
```

The active section should be visually distinguished.

---

## Header

The header can contain:

```text
Page Title
Search
Notifications
Connection Status
User Profile
```

---

## Statistics Cards

```text
+----------------+ +----------------+ +----------------+
| Upcoming       | | Invitations    | | Conflicts      |
| Events         | | Processed      | | Detected       |
|                | |                | |                |
|      12        | |       8        | |       2        |
+----------------+ +----------------+ +----------------+
```

---

# Events Page

The Events page provides a detailed view of scheduled events.

```text
+----------------------------------------------------------------+
| Events                                                         |
+----------------------------------------------------------------+
| Search events...                  Filter    Date    Status      |
+----------------------------------------------------------------+
|                                                                |
| Event                  Date             Time       Status       |
|                                                                |
| Team Meeting           Oct 10           10:00      Scheduled    |
| Client Call            Oct 11           13:00      Scheduled    |
| Project Review         Oct 12           15:00      Conflict     |
| Product Demo           Oct 13           11:00      Scheduled    |
|                                                                |
+----------------------------------------------------------------+
```

Possible statuses:

```text
Scheduled
Pending
Conflict
Cancelled
Completed
Failed
```

---

# Invitations Page

The Invitations page displays incoming event invitations processed by meetFlow.

```text
+----------------------------------------------------------------+
| Invitations                                                    |
+----------------------------------------------------------------+
|                                                                |
| From             Event              Date          Status        |
|                                                                |
| John Doe         Team Meeting       Oct 10        Processed     |
| Jane Smith       Client Call        Oct 11        Scheduled     |
| Company Ltd      Project Review     Oct 12        Conflict      |
|                                                                |
+----------------------------------------------------------------+
```

An invitation details view can display:

```text
Invitation Details

Event:
Project Review

Organizer:
John Doe

Start:
October 12, 2026 15:00

End:
October 12, 2026 16:00

Processing Status:
Conflict

Reason:
Existing calendar event detected.

Notification:
WhatsApp alert sent.
```

---

# Activity Page

The Activity page provides an audit trail.

```text
+----------------------------------------------------------------+
| Activity                                                       |
+----------------------------------------------------------------+
| Time       Event                           Status               |
+----------------------------------------------------------------+
| 10:02      Gmail invitation received      Processed            |
| 10:03      Calendar availability checked  Available            |
| 10:03      Calendar event created         Success              |
| 11:15      Calendar conflict detected    Conflict              |
| 11:15      WhatsApp alert dispatched      Success              |
+----------------------------------------------------------------+
```

---

# Integrations Page

The Integrations page manages external services.

```text
+----------------------------------------------------------------+
| Integrations                                                   |
+----------------------------------------------------------------+
|                                                                |
| Google Account                         Connected               |
| Gmail                                  Connected               |
| Google Calendar                        Connected               |
| Google Cloud Pub/Sub                   Connected               |
| Twilio                                  Connected               |
| WhatsApp                                Connected               |
|                                                                |
+----------------------------------------------------------------+
```

Possible states:

```text
Connected
Disconnected
Connecting
Error
Requires Authentication
```

---

# Settings Page

Possible configuration areas:

```text
Account Settings

Notification Settings

Scheduling Rules

Calendar Preferences

WhatsApp Preferences

Integration Settings

Security Settings
```

Example scheduling controls:

```text
Automatically schedule free invitations
[ ON ]

Notify me when conflicts occur
[ ON ]

Send WhatsApp alerts
[ ON ]

Automatically process recurring invitations
[ OFF ]
```

---

# Responsive Design

The application should support:

```text
Desktop
Tablet
Mobile
```

Desktop:

```text
+----------+--------------------------------+
| Sidebar  | Main Content                   |
|          |                                |
|          | Dashboard                      |
|          |                                |
+----------+--------------------------------+
```

Mobile:

```text
+--------------------------------+
| meetFlow              Menu     |
+--------------------------------+
|                                |
| Dashboard                      |
|                                |
| Statistics                     |
|                                |
| Today's Events                 |
|                                |
| Recent Activity                |
|                                |
+--------------------------------+
```

On smaller screens, the sidebar should collapse into a mobile navigation menu.

---

# Frontend Data Flow

```text
                     Frontend
                        |
                        v
              +-------------------+
              | JavaScript Client |
              +---------+---------+
                        |
                        v
                    REST API
                        |
                        v
              +-------------------+
              | Express Backend   |
              +---------+---------+
                        |
          +-------------+-------------+
          |             |             |
          v             v             v
       Gmail        Calendar        Twilio
        API           API            API
```

The frontend should never expose Google or Twilio credentials.

Sensitive operations must remain on the backend.

---

# Authentication Flow

```text
User
 |
 v
Login with Google
 |
 v
Google OAuth
 |
 v
Authorization
 |
 v
OAuth Callback
 |
 v
Express Backend
 |
 v
Authenticated Session
 |
 v
Dashboard
```

---

# Scheduling Decision Engine

```text
                Event Invitation
                       |
                       v
                Validate Data
                       |
                       v
               Extract Date/Time
                       |
                       v
             Check Calendar FreeBusy
                       |
                +------+------+
                |             |
                v             v
              FREE           BUSY
                |             |
                v             v
         Create Event     Notify User
                |             |
                v             v
          Log Success     Log Conflict
                |             |
                +------+------+
                       |
                       v
                Update Dashboard
```

The scheduling engine should remain independent from the frontend so that automated scheduling can continue even when the dashboard is not open.

---

# AI Agent Architecture

AI capabilities can be introduced as an additional workflow layer.

The AI layer should assist with understanding and processing information while deterministic application logic remains responsible for critical scheduling operations.

```text
                    Incoming Invitation
                            |
                            v
                     Invitation Parser
                            |
                            v
                     AI Agent Layer
                            |
             +--------------+--------------+
             |              |              |
             v              v              v
       Extract Event   Understand Intent   Identify
          Details                         Workflow
             |              |              |
             +--------------+--------------+
                            |
                            v
                    Scheduling Engine
                            |
                            v
                    Calendar / Twilio
```

Potential AI capabilities:

* Understanding natural-language invitations
* Extracting event information
* Identifying scheduling intent
* Classifying invitations
* Generating notification content
* Assisting with workflow decisions
* Detecting ambiguous event information
* Asking for clarification when required

The AI layer should not bypass calendar validation or security controls.

---

# Complete Platform Workflow

```text
+-------------------------------------------------------------------+
|                           meetFlow                               |
+-------------------------------------------------------------------+
|                                                                   |
|                         USER                                      |
|                           |                                       |
|                           v                                       |
|                     Google OAuth                                  |
|                           |                                       |
|                           v                                       |
|                    Scheduling Dashboard                           |
|                           |                                       |
|                           v                                       |
|                    Express.js Backend                             |
|                           |                                       |
|          +----------------+----------------+                       |
|          |                |                |                       |
|          v                v                v                       |
|       Gmail           Calendar          Twilio                    |
|          |                |                |                       |
|          v                |                |                       |
|       Pub/Sub             |                |                       |
|          |                |                |                       |
|          +----------------+----------------+                       |
|                           |                                       |
|                           v                                       |
|                   Workflow Engine                                 |
|                           |                                       |
|                           v                                       |
|                  Invitation Parser                                |
|                           |                                       |
|                           v                                       |
|                   Scheduling Engine                               |
|                           |                                       |
|                    +------+-------+                               |
|                    |              |                               |
|                    v              v                               |
|                  FREE           BUSY                              |
|                    |              |                               |
|                    v              v                               |
|              Create Event    WhatsApp Alert                       |
|                    |              |                               |
|                    +------+-------+                               |
|                           |                                       |
|                           v                                       |
|                    Activity Logger                                |
|                           |                                       |
|                           v                                       |
|                    Dashboard Update                               |
|                                                                   |
+-------------------------------------------------------------------+
```

---

# API Architecture

Suggested backend routes:

```text
GET  /
GET  /auth/google
GET  /oauth2callback

POST /webhooks/pubsub

GET  /api/events
GET  /api/status
GET  /api/invitations
GET  /api/activity

POST /api/schedule
POST /api/invitations/process

GET  /api/integrations
POST /api/integrations/google
POST /api/integrations/twilio
```

The API should be organized around application resources rather than exposing external API operations directly to the frontend.

---

# Google Cloud Setup

The following Google APIs/services are required:

* Gmail API
* Google Calendar API
* Google Cloud Pub/Sub API

Create Google OAuth 2.0 credentials.

Local redirect URI:

```text
http://localhost:3000/oauth2callback
```

Production redirect URI:

```text
https://your-domain.com/oauth2callback
```

---

# Gmail Push Notification Architecture

The Gmail monitoring workflow is:

```text
Gmail Mailbox
     |
     v
Gmail Watch
     |
     v
Google Cloud Pub/Sub
     |
     v
meetFlow Webhook
     |
     v
Gmail History API
     |
     v
New Message
     |
     v
Invitation Parser
```

The application should retrieve the actual Gmail message after receiving the Pub/Sub notification rather than treating the Pub/Sub message itself as the complete email content.

---

# Google Calendar FreeBusy

Calendar availability is checked before creating an event.

Example:

```javascript
const response = await calendar.freebusy.query({
  requestBody: {
    timeMin: startTime,
    timeMax: endTime,
    items: [
      {
        id: "primary"
      }
    ]
  }
});
```

The result determines whether the requested period is available.

```text
FREE
 |
 v
Create Calendar Event


BUSY
 |
 v
Do Not Create Duplicate Event
 |
 v
Send WhatsApp Alert
```

---

# Twilio Configuration

Environment variables:

```env
TWILIO_ACCOUNT_SID=
TWILIO_AUTH_TOKEN=
TWILIO_WHATSAPP_FROM=
TWILIO_WHATSAPP_TO=
```

The Twilio credentials must remain server-side.

---

# Environment Configuration

Complete `.env` example:

```env
PORT=3000

# Google OAuth
GOOGLE_CLIENT_ID=your_google_client_id
GOOGLE_CLIENT_SECRET=your_google_client_secret
GOOGLE_REDIRECT_URI=http://localhost:3000/oauth2callback

# Google Cloud
GOOGLE_PROJECT_ID=your_google_cloud_project_id
PUBSUB_TOPIC=your_pubsub_topic
PUBSUB_VERIFICATION_TOKEN=your_pubsub_verification_token

# Twilio
TWILIO_ACCOUNT_SID=your_twilio_account_sid
TWILIO_AUTH_TOKEN=your_twilio_auth_token
TWILIO_WHATSAPP_FROM=whatsapp:+14155238886
TWILIO_WHATSAPP_TO=whatsapp:+234XXXXXXXXXX

# Application
SESSION_SECRET=replace_with_a_secure_random_value
```

---

# Installation

Clone the repository:

```bash
git clone https://github.com/your-username/meetFlow.git
```

Move into the project:

```bash
cd meetFlow
```

Install dependencies:

```bash
npm install
```

Create the environment file:

```bash
cp .env.example .env
```

On Windows PowerShell, you can use:

```powershell
Copy-Item .env.example .env
```

Configure the required credentials inside `.env`.

---

# Running the Application

Start the application:

```bash
npm start
```

For development:

```bash
npm run dev
```

The application will normally be available at:

```text
http://localhost:3000
```

---

# End-to-End Testing

## Test 1: Available Calendar

Expected workflow:

```text
Invitation arrives
       |
       v
Pub/Sub notification
       |
       v
Retrieve Gmail message
       |
       v
Parse invitation
       |
       v
Check FreeBusy
       |
       v
Calendar is FREE
       |
       v
Create event
       |
       v
Log success
       |
       v
Update dashboard
```

Expected result:

```text
Event created successfully.
```

---

## Test 2: Calendar Conflict

Expected workflow:

```text
Invitation arrives
       |
       v
Pub/Sub notification
       |
       v
Retrieve Gmail message
       |
       v
Parse invitation
       |
       v
Check FreeBusy
       |
       v
Calendar is BUSY
       |
       v
Do not create duplicate event
       |
       v
Send WhatsApp alert
       |
       v
Log conflict
       |
       v
Update dashboard
```

Expected result:

```text
Calendar conflict detected.
WhatsApp notification sent.
No duplicate event created.
```

---

# Testing Checklist

## Authentication

* [ ] Google OAuth login
* [ ] OAuth callback
* [ ] Invalid OAuth handling
* [ ] Session handling

## Gmail

* [ ] Gmail API connection
* [ ] Gmail watch
* [ ] Pub/Sub topic
* [ ] Pub/Sub webhook
* [ ] Gmail message retrieval
* [ ] Invitation parsing

## Calendar

* [ ] Start time extraction
* [ ] End time extraction
* [ ] FreeBusy request
* [ ] Free calendar event creation
* [ ] Conflict detection
* [ ] Duplicate event prevention

## Twilio

* [ ] Twilio authentication
* [ ] WhatsApp message
* [ ] Notification failure handling
* [ ] Delivery status logging

## Dashboard

* [ ] Event display
* [ ] Invitation display
* [ ] Activity logs
* [ ] Integration status
* [ ] Connection status
* [ ] Responsive layout

## Security

* [ ] `.env` excluded from Git
* [ ] API credentials protected
* [ ] OAuth tokens protected
* [ ] HTTPS in production
* [ ] Webhook validation
* [ ] Input validation
* [ ] Rate limiting
* [ ] Secure logging
* [ ] Least-privilege OAuth scopes

---

# Security Architecture

Sensitive information must never be exposed to the frontend or committed to Git.

Protected information includes:

```text
Google Client Secret
Google Access Tokens
Google Refresh Tokens
Twilio Auth Token
API Keys
Session Secrets
Webhook Secrets
```

Security principles:

```text
Frontend
   |
   | Public requests only
   v
Express Backend
   |
   | Authenticated server-side requests
   v
External APIs
```

The backend acts as the security boundary between the browser and external services.

---

# Production Architecture

A production deployment can use the following architecture:

```text
                         INTERNET
                            |
                            v
                    HTTPS / Domain
                            |
                            v
                    Reverse Proxy
                            |
                            v
                     Node.js Server
                            |
                +-----------+-----------+
                |           |           |
                v           v           v
             Gmail      Calendar      Twilio
                |           |           |
                +-----------+-----------+
                            |
                            v
                    Google Pub/Sub
```

Production infrastructure should include:

* HTTPS
* Secure environment variables
* Process management
* Logging
* Monitoring
* Error tracking
* Rate limiting
* Secure sessions
* Webhook validation
* Database or persistent storage when required
* Backup and recovery procedures

---

# Future Database Architecture

As meetFlow grows, persistent storage can be introduced.

A possible database structure:

```text
users
 |
 +-- integrations
 |
 +-- events
 |
 +-- invitations
 |
 +-- activity_logs
 |
 +-- notifications
```

Example relationship:

```text
User
 |
 +---- Events
 |
 +---- Invitations
 |
 +---- Integrations
 |
 +---- Activity Logs
 |
 +---- Notifications
```

This would allow the application to maintain historical records beyond the current process memory.

---

# Development Roadmap

## Phase 1: Foundation

* [ ] Initialize Node.js project
* [ ] Configure Express
* [ ] Configure environment variables
* [ ] Create frontend
* [ ] Create repository structure
* [ ] Configure Git

## Phase 2: Google Integration

* [ ] Google OAuth
* [ ] Gmail API
* [ ] Google Calendar API
* [ ] Google Cloud Pub/Sub
* [ ] Gmail watch

## Phase 3: Scheduling Engine

* [ ] Invitation parser
* [ ] Event extraction
* [ ] FreeBusy integration
* [ ] Conflict detection
* [ ] Automatic event creation

## Phase 4: Notifications

* [ ] Twilio integration
* [ ] WhatsApp messaging
* [ ] Conflict notifications
* [ ] Notification logging

## Phase 5: Scheduling Dashboard

* [ ] Dashboard
* [ ] Events page
* [ ] Invitations page
* [ ] Activity page
* [ ] Integrations page
* [ ] Settings page
* [ ] Responsive design

## Phase 6: AI Agent Automation

* [ ] AI invitation understanding
* [ ] Natural-language event extraction
* [ ] Intent classification
* [ ] Workflow assistance
* [ ] Intelligent notification generation
* [ ] Ambiguous invitation handling

## Phase 7: Production

* [ ] Database
* [ ] Production deployment
* [ ] HTTPS
* [ ] Monitoring
* [ ] Error tracking
* [ ] Automated testing
* [ ] Security audit
* [ ] Performance optimization

---

# Git Workflow

Create a feature branch:

```bash
git switch -c feature/your-feature
```

Check changes:

```bash
git status
```

Stage changes:

```bash
git add .
```

Commit changes:

```bash
git commit -m "Add your feature"
```

Push the branch:

```bash
git push -u origin feature/your-feature
```

For subsequent changes:

```bash
git add .
git commit -m "Describe what changed"
git push
```

---

# Repository Development Rules

The repository should follow these principles:

1. Keep frontend and backend responsibilities separated.
2. Keep external integrations inside `services/`.
3. Keep reusable logic inside `utils/`.
4. Avoid putting all application logic inside `server.js`.
5. Never commit secrets.
6. Use meaningful commit messages.
7. Create feature branches for major changes.
8. Validate external API responses.
9. Log important automated actions.
10. Write tests for critical scheduling workflows.

---

# Recommended Long-Term Architecture

As the project becomes larger, the repository can evolve from the initial structure into a more scalable architecture:

```text
meetFlow/
|
├── src/
|   |
|   ├── config/
|   |   ├── env.js
|   |   └── google.js
|   |
|   ├── controllers/
|   |   ├── auth.controller.js
|   |   ├── event.controller.js
|   |   └── webhook.controller.js
|   |
|   ├── routes/
|   |   ├── auth.routes.js
|   |   ├── event.routes.js
|   |   └── webhook.routes.js
|   |
|   ├── services/
|   |   ├── gmail.service.js
|   |   ├── calendar.service.js
|   |   ├── pubsub.service.js
|   |   └── twilio.service.js
|   |
|   ├── workflows/
|   |   ├── invitation.workflow.js
|   |   ├── scheduling.workflow.js
|   |   └── notification.workflow.js
|   |
|   ├── agents/
|   |   ├── invitation.agent.js
|   |   └── scheduling.agent.js
|   |
|   ├── utils/
|   |   ├── parser.js
|   |   ├── logger.js
|   |   └── validator.js
|   |
|   └── server.js
|
├── public/
|   ├── index.html
|   ├── style.css
|   └── app.js
|
├── tests/
|   ├── unit/
|   ├── integration/
|   └── e2e/
|
├── .env
├── .env.example
├── .gitignore
├── package.json
├── package-lock.json
└── README.md
```

This architecture separates:

```text
Routes
   |
   v
Controllers
   |
   v
Workflows
   |
   +-------------------+
   |                   |
   v                   v
Services             AI Agents
   |                   |
   +---------+---------+
             |
             v
       External APIs
```

This makes the application easier to test, maintain, debug, and extend.

---

# Project Status

```text
Status: Active Development
```

The project is currently focused on establishing the core scheduling workflow, integrations, dashboard, automation engine, and AI-assisted capabilities.

---

# License

Add the project's license information here.

Example:

```text
MIT License
```

---

# Author

Success Azibapu aka Linostudiong

---

# Project Summary

meetFlow connects incoming event invitations with calendar availability and automated notifications.

The platform follows this fundamental workflow:

```text
Understand the invitation.
        |
        v
Extract the event.
        |
        v
Check the schedule.
        |
        v
Automate the decision.
        |
        v
Create the event or notify the user.
        |
        v
Record what happened.
```

The long-term goal is to evolve meetFlow into an intelligent event automation platform capable of understanding invitations, managing scheduling workflows, communicating conflicts, and providing users with a centralized view of their scheduling activity.
