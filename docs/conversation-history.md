11/25/2025 - ChatGPT 5.1

Below is a clean, consolidated **Markdown export** containing the updated product plan and the technical architecture diagram rewritten for a mobile-friendly website with both creator and consumer user types.

---

# VTuber Scheduling Platform – Product Plan & Technical Architecture

## Overview

A mobile-friendly web platform enabling VTuber content creators to schedule livestreams and enabling fans (content consumers) to follow schedules across YouTube, Twitch, and X/Twitter.

---

## User Types

### 1. Content Creators

Creators use the platform to:

* Schedule livestreams manually.
* Import scheduled livestreams automatically via OAuth with YouTube/Twitch.
* Edit, update, and manage stream metadata.
* Publish a sharable schedule page for their audience.
* Publish schedule "posters" (GIF, JPEG images) to X/Twitter, YouTube, and Twitch (optional post-MVP).
* Track engagement metrics (optional post-MVP).
* Automatically read schedule information from creator-uploaded "poster" GIFs and JPEGs (optional post-MVP).

### 2. Content Consumers

Fans use the platform to:

* Discover VTubers.
* Follow their favorite creators.
* View upcoming livestream schedules across platforms.
* Receive browser push or email notifications.
* Maintain a personalized watchlist and schedule view.
* Automatically read schedule information from "poster" GIFs and JPEGs on X/Twitter (optional post-MVP).

---

## Core Features

### Creator-Facing Features

* OAuth with YouTube/Twitch/X/Twitter (channel linking + permissions).
* Auto-import scheduled livestreams.
* Manual scheduling tool (title, date, platform, thumbnail, "poster" image).
* Event editing, cancellation, and rescheduling.
* Creator dashboard with event list and stats.
* Public schedule page with mobile-first layout.

### Consumer-Facing Features

* Search and follow VTubers.
* Mobile-optimized feed of upcoming livestreams.
* Timezone-aware schedule presentation.
* Browser push notifications.
* Optional email reminders.
* Filtering by creator, platform, tags, or date.

### Platform Features

* Unified stream aggregation across multiple APIs.
* Scalable notification service.
* Server-side caching for fast mobile browsing.
* Roles and permissions for creator vs. consumer accounts.

---

## Technical Architecture

### High-Level Components

* **Mobile-Friendly Web Client:** Responsive UI, offline-first browsing.
* **API Gateway / Web Server:** Routing, authentication, rate limiting.
* **Backend Services:** GitHub, Vercel, Supabase.

  * User & Creator Service
  * Stream Aggregation Service
  * Creator Scheduling Service
  * Notification & Event Trigger Service

* **Data Layer:** PostgreSQL for persistent data, Redis for cached schedules.
* **External Integrations:** YouTube Data API v3, Twitch Helix API, X/Twitter API.
* **Notification Infrastructure:** Email provider, Web Push API.
* **CI/CD + Cloud Infrastructure:** Automated deployment, monitoring, logs.

---

## Architecture Diagram (Mermaid)

```mermaid
graph TD

%% User Layers
A1[Content Consumers
- Mobile-friendly Web UI
- Follow Creators
- View Schedules
- Notifications]
A2[Content Creators
- Web Dashboard
- Manage Streams
- OAuth Integration]

A1 --> B[Web Server / API Gateway]
A2 --> B

%% Backend Services
B --> C1[User & Creator Service
- Profiles
- Roles
- Preferences
- Follow Lists]
B --> C2[Stream Aggregation Service
- YouTube API Polling
- Twitch API Polling
- X/Twitter API Polling
- Metadata Normalization]
B --> C3[Creator Scheduling Service
- Manual Event Creation
- OAuth Sync
- Stream Editing]
B --> C4[Notification & Scheduling Service
- Email
- Browser Push
- Event Triggering]

%% External APIs
C2 --> D1[YouTube Data API v3]
C2 --> D2[Twitch Helix API]
C3 --> D1
C3 --> D2

%% Data Layer
C1 --> E[(PostgreSQL
- Users
- Creator Accounts
- Events
- Follows)]
C2 --> E
C3 --> E
C4 --> E

%% Caching
C2 --> F[(Redis Cache
- Upcoming Streams
- Poll Results)]
C4 --> F

%% Notification Infrastructure
C4 --> G1[Email Provider]
C4 --> G2[Web Push API]

%% CI/CD and Hosting
H[CI/CD Pipeline
- Testing
- Deployment
- Monitoring] --> B
H --> C1
H --> C2
H --> C3
H --> C4

subgraph Cloud Provider (AWS/GCP)
B
C1
C2
C3
C4
E
F
end
```
