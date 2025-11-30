# VTuber Scheduling Platform - Product Requirements Document

**Version:** 1.0  
**Last Updated:** November 30, 2025  
**Status:** Active Development - MVP Phase

---

## Table of Contents

1. [Executive Summary](#executive-summary)
2. [Product Vision & Goals](#product-vision--goals)
3. [User Personas](#user-personas)
4. [MVP Feature Specifications](#mvp-feature-specifications)
5. [User Stories & Acceptance Criteria](#user-stories--acceptance-criteria)
6. [Technical Architecture](#technical-architecture)
7. [Database Schema](#database-schema)
8. [API Specifications](#api-specifications)
9. [Authentication & Authorization](#authentication--authorization)
10. [Service Setup Instructions](#service-setup-instructions)
11. [Deployment Strategy](#deployment-strategy)
12. [Success Metrics](#success-metrics)
13. [Post-MVP Roadmap](#post-mvp-roadmap)

---

## Executive Summary

### Problem Statement
VTuber content creators struggle to manage and communicate their streaming schedules across multiple platforms (YouTube, Twitch). Fans find it difficult to track their favorite creators' schedules without manually checking each platform individually.

### Solution
A mobile-friendly web platform that enables VTubers to manage livestream schedules in one place and allows fans to follow multiple creators with personalized notifications and unified schedule views.

### Target Audience
- **Primary:** VTuber content creators (estimated <5,000 at launch)
- **Secondary:** VTuber fans and content consumers (estimated <10,000 at launch)

### Core Value Propositions
- **For Creators:** Centralized schedule management, automatic stream verification, shareable schedule pages
- **For Consumers:** Unified view of all followed creators, customizable notifications, timezone-aware schedules

---

## Product Vision & Goals

### Vision Statement
To become the central hub for VTuber community scheduling, making it effortless for creators to share their schedules and fans to never miss a stream.

### MVP Goals
1. Enable creators to create and manage stream schedules with YouTube/Twitch verification
2. Allow users to discover, follow, and track favorite creators
3. Provide customizable notification system (opt-in)
4. Deliver mobile-optimized, timezone-aware schedule views
5. Support concurrent multi-platform streams

### Success Criteria
- 100+ active creators within 3 months of launch
- 500+ active consumers within 3 months
- 70%+ user retention after first month
- <2 second page load times on mobile

---

## User Personas

### Persona 1: Content Creator (Primary)
**Name:** Sarah "VtuberSarah"  
**Role:** Mid-tier VTuber with 10K YouTube subscribers, 3K Twitch followers  
**Goals:**
- Maintain consistent streaming schedule across platforms
- Reduce manual schedule posting on social media
- Track follower engagement with schedule

**Pain Points:**
- Managing schedules on multiple platforms is time-consuming
- Fans miss streams due to timezone confusion
- Last-minute schedule changes are hard to communicate

**Needs:**
- Quick schedule creation/editing
- Automatic link verification
- Shareable schedule page

### Persona 2: VTuber Fan (Primary)
**Name:** Mike  
**Role:** Follows 15+ VTubers across YouTube and Twitch  
**Goals:**
- Never miss streams from favorite creators
- See all upcoming streams in one place
- Get reminders before streams start

**Pain Points:**
- Checking multiple platforms for schedules is tedious
- Timezone conversions are confusing
- Forgets about scheduled streams

**Needs:**
- Unified schedule view
- Customizable notifications
- Easy creator discovery

### Persona 3: Creator-Consumer Hybrid
**Name:** Alex "AlexPlays"  
**Role:** Small VTuber (500 subs) who also watches other VTubers  
**Goals:**
- Manage own streaming schedule
- Follow and support fellow VTubers
- Coordinate collab streams

**Needs:**
- Both creator and consumer features in one account
- Ability to edit own schedule and view others'

---

## MVP Feature Specifications

### F1: User Authentication & Account Management

**Description:** Users can sign up and log in using social OAuth from YouTube/Google, Twitch, or X/Twitter.

**Requirements:**
- OAuth integration with YouTube/Google, Twitch, X/Twitter via Supabase Auth
- Single account supports both creator and consumer roles
- User profile with basic information (username, avatar, timezone, notification preferences)
- Timezone detection and manual override

**Technical Notes:**
- Use Supabase Auth with social providers
- Store OAuth tokens securely for API access
- Implement role-based access control (RBAC)

---

### F2: Creator Schedule Management

**Description:** Creators can create, edit, and delete stream schedules for YouTube and/or Twitch.

**Requirements:**

**Manual Schedule Creation:**
- Required fields: Stream title, date/time (in creator's timezone), platform(s), stream URL
- Optional fields: Description, thumbnail URL, tags/categories
- Multi-platform support (one schedule entry can represent concurrent streams)
- Link verification for YouTube and Twitch URLs before publishing

**Publishing Options:**
- Auto-publish: Schedules automatically go live upon creation
- Manual publish: Schedules saved as draft, published by creator

**Editing & Deletion:**
- Edit any schedule field after creation
- Delete scheduled streams
- Cancellation status (mark as "Cancelled" without deletion)
- Change history/audit log for schedule modifications

**Validation:**
- Verify YouTube URLs match pattern: `youtube.com/watch?v=` or `youtu.be/`
- Verify Twitch URLs match pattern: `twitch.tv/[username]`
- API verification: Check if stream/channel exists via YouTube Data API v3 or Twitch Helix API
- Date/time validation (must be in future, within 90-day window)

**Technical Notes:**
- YouTube Data API v3: `videos.list` endpoint for stream verification
- Twitch Helix API: `streams` and `channels` endpoints
- Store verification status and last verified timestamp
- Re-verify links periodically (daily cron job)

---

### F3: Consumer - Follow & Track Creators

**Description:** Users can search for creators by YouTube/Twitch username, follow them, and view their schedules.

**Requirements:**

**Creator Discovery (MVP):**
- Search by YouTube channel name/handle
- Search by Twitch username
- API verification that creator exists on platform
- Display creator profile (name, avatar from platform, platform links)

**Following:**
- One-click follow/unfollow
- Follow list management (view all followed creators)
- Follow count display for creators

**Schedule Viewing:**
- Aggregated feed of all followed creators' upcoming streams
- Sort by date/time (chronological)
- Filter by creator, platform, date range
- Timezone-aware display (user's local time)
- Color-coded by platform (YouTube red, Twitch purple)

**Technical Notes:**
- YouTube Data API v3: `channels.list` or `search.list` for creator lookup
- Twitch Helix API: `users` endpoint for username search
- Cache creator metadata (name, avatar) to reduce API calls
- Real-time updates when creator publishes new schedule

---

### F4: Collaborative Schedule Editing

**Description:** All users can suggest edits to schedules of creators they follow. Creators approve/reject edits.

**Requirements:**

**Edit Suggestions (Consumer):**
- Consumers can suggest corrections to stream details (time, title, URL)
- Suggestion includes reason/note field
- Pending suggestions visible to creator only

**Edit Management (Creator):**
- View pending edit suggestions
- Approve (apply changes) or reject suggestions
- Optional: Thank/message suggester

**Use Cases:**
- Fan notices creator posted wrong stream time on Twitter vs. actual YouTube scheduled time
- Community helps correct typos or broken links
- Collaborative schedule maintenance

**Technical Notes:**
- Store edit suggestions as separate records with status (pending/approved/rejected)
- Notification to creator when edit suggested
- Track suggestion author for reputation (future feature)

---

### F5: Notification System

**Description:** Users receive notifications for upcoming streams from followed creators.

**Requirements:**

**Notification Types (MVP):**
- Browser push notifications (Web Push API)
- Email notifications (via Resend)

**Notification Settings (User-Specific):**
- Global toggle: Enable/disable all notifications (default: OFF - opt-in)
- Notification timing: Options include:
  - 30 minutes before stream
  - 1 hour before stream
  - 1 day before stream
  - At stream start time
- Per-creator notification override (follow creator but mute notifications)
- Email vs. push notification preference

**Notification Triggers:**
- New stream scheduled by followed creator
- Schedule change (time update, cancellation)
- Upcoming stream reminder (based on user timing preference)

**Technical Notes:**
- Use Resend for email notifications (transactional emails)
- Web Push API for browser notifications (requires user permission)
- Background job/cron to check upcoming streams and send reminders
- Rate limiting: Max 1 email per hour per user to prevent spam
- Supabase Realtime for instant push notifications (optional enhancement)

---

### F6: Public Creator Schedule Pages

**Description:** Each creator has a shareable public URL displaying their upcoming streams.

**Requirements:**
- Public URL format: `app.domain.com/creator/[username]` or `app.domain.com/c/[username]`
- Display creator profile (name, avatar, bio, platform links)
- List of upcoming streams (next 30 days)
- Past streams archive (optional toggle)
- Mobile-responsive design
- No login required to view

**Technical Notes:**
- Server-side rendering (SSR) for SEO
- Open Graph meta tags for social sharing
- Canonical URLs for each creator

---

### F7: Dashboard Views

**Creator Dashboard:**
- List of all scheduled streams (upcoming, drafts, past)
- Quick stats: Total followers, upcoming streams count
- Pending edit suggestions
- Quick create button

**Consumer Dashboard:**
- Feed of upcoming streams from followed creators
- Calendar view (optional)
- Filters: Platform, creator, date range
- Quick access to followed creators list

---

## User Stories & Acceptance Criteria

### US-1: User Sign Up & Login
**As a** new user  
**I want to** sign up using my YouTube, Twitch, or Twitter account  
**So that** I can quickly create an account without remembering a new password

**Acceptance Criteria:**
- [ ] User can click "Sign in with YouTube" and complete OAuth flow
- [ ] User can click "Sign in with Twitch" and complete OAuth flow
- [ ] User can click "Sign in with Twitter/X" and complete OAuth flow
- [ ] User profile is created with data from OAuth provider (name, avatar)
- [ ] User is redirected to onboarding/dashboard after first login
- [ ] Returning users are redirected to dashboard
- [ ] OAuth tokens are securely stored in Supabase

---

### US-2: Creator Creates Manual Schedule Entry
**As a** content creator  
**I want to** manually create a stream schedule entry  
**So that** my followers know when I'll be live

**Acceptance Criteria:**
- [ ] Creator can access "New Schedule" form
- [ ] Form includes: Title, Date, Time, Platform(s), Stream URL, Description (optional)
- [ ] Date picker shows calendar, time picker shows creator's timezone
- [ ] Platform selection allows multiple (YouTube + Twitch simultaneously)
- [ ] Stream URL is validated against YouTube/Twitch URL patterns
- [ ] URL is verified via API to confirm stream/channel exists
- [ ] Creator can choose "Publish Now" or "Save as Draft"
- [ ] Published schedules appear immediately in follower feeds
- [ ] Success/error messages are displayed

---

### US-3: Consumer Searches for Creator by Username
**As a** consumer  
**I want to** search for a VTuber by their YouTube or Twitch username  
**So that** I can follow them and see their schedule

**Acceptance Criteria:**
- [ ] Search bar accepts YouTube channel name/handle or Twitch username
- [ ] Search queries YouTube and Twitch APIs in parallel
- [ ] Results show creator name, avatar, platform(s), follower count
- [ ] If creator exists on platform but not in app, show "Add to Platform" option
- [ ] Clicking "Follow" adds creator to user's follow list
- [ ] Follow button toggles to "Following" with unfollow option
- [ ] Error message if creator not found on any platform

---

### US-4: Consumer Views Aggregated Schedule Feed
**As a** consumer  
**I want to** see all upcoming streams from creators I follow in one place  
**So that** I don't miss any content

**Acceptance Criteria:**
- [ ] Dashboard displays chronological list of upcoming streams
- [ ] Each stream shows: Creator name/avatar, title, platform(s), time (user's timezone), link
- [ ] Timezone is auto-detected or manually set by user
- [ ] Streams are color-coded by platform (YouTube, Twitch)
- [ ] Filter options: Platform, Creator, Date range
- [ ] Clicking stream links to external platform (YouTube/Twitch)
- [ ] "No upcoming streams" message if follow list is empty
- [ ] Real-time updates when new streams are published

---

### US-5: User Enables Notifications
**As a** user  
**I want to** receive notifications before streams start  
**So that** I don't forget to watch

**Acceptance Criteria:**
- [ ] User can access notification settings
- [ ] Toggle to enable browser push notifications (requests permission)
- [ ] Toggle to enable email notifications
- [ ] Dropdown to select timing: 30 min, 1 hour, 1 day before, or at start
- [ ] Per-creator mute option (follow but don't notify)
- [ ] Default is notifications OFF (opt-in)
- [ ] Test notification button to verify setup
- [ ] Changes save immediately

---

### US-6: Consumer Suggests Schedule Edit
**As a** consumer  
**I want to** suggest a correction to a creator's schedule  
**So that** the community has accurate information

**Acceptance Criteria:**
- [ ] "Suggest Edit" button on each schedule entry
- [ ] Form shows current values and allows edits
- [ ] Required: Reason for edit (text field)
- [ ] Suggestion is saved as "Pending" status
- [ ] Creator receives notification of pending suggestion
- [ ] Consumer sees confirmation message
- [ ] Consumer can view their pending suggestions

---

### US-7: Creator Reviews and Approves Edit Suggestion
**As a** creator  
**I want to** review edit suggestions from my followers  
**So that** I can fix mistakes and maintain accurate schedules

**Acceptance Criteria:**
- [ ] Creator dashboard shows count of pending suggestions
- [ ] Suggestions list shows: Suggester, original vs. proposed values, reason
- [ ] "Approve" button applies changes and marks as approved
- [ ] "Reject" button discards suggestion and marks as rejected
- [ ] Approved changes update the schedule immediately
- [ ] Suggester receives notification of approval/rejection (optional MVP)

---

### US-8: Creator Publishes Schedule to Public Page
**As a** creator  
**I want to** share a public link to my schedule  
**So that** anyone can see my upcoming streams without logging in

**Acceptance Criteria:**
- [ ] Public URL is auto-generated: `/creator/[username]`
- [ ] Page displays creator profile: Name, avatar, bio, platform links
- [ ] List of upcoming streams (next 30 days)
- [ ] No login required to view
- [ ] Mobile-responsive design
- [ ] Social sharing meta tags (Open Graph)
- [ ] Option to copy link to clipboard

---

## Technical Architecture

### Technology Stack

**Frontend:**
- **Framework:** Next.js 14+ (App Router, React Server Components)
- **UI Library:** Tailwind CSS + shadcn/ui components
- **State Management:** React Context + Server State (SWR or TanStack Query)
- **PWA:** Next-PWA for progressive web app capabilities

**Backend:**
- **Framework:** Next.js API Routes (Edge & Node.js runtime)
- **Database:** Supabase PostgreSQL
- **Authentication:** Supabase Auth with OAuth providers
- **Real-time:** Supabase Realtime (for live updates)
- **Background Jobs:** Vercel Cron Jobs or Inngest

**External Services:**
- **Hosting:** Vercel (Frontend + API)
- **Database & Auth:** Supabase
- **Email:** Resend
- **APIs:** YouTube Data API v3, Twitch Helix API, X/Twitter API v2

**Developer Tools:**
- **Version Control:** Git + GitHub
- **CI/CD:** Vercel GitHub integration (auto-deploy)
- **Code Quality:** ESLint, Prettier, TypeScript
- **Testing:** Jest, React Testing Library, Playwright (E2E)

---

### System Architecture Diagram

```
┌─────────────────────────────────────────────────────────────┐
│                        CLIENT LAYER                         │
│                                                               │
│  ┌─────────────────┐         ┌─────────────────┐           │
│  │  Web Browser    │         │  Mobile Browser  │           │
│  │  (Desktop)      │         │  (iOS/Android)   │           │
│  └────────┬────────┘         └────────┬────────┘           │
│           │                            │                     │
│           └──────────┬─────────────────┘                     │
│                      │                                       │
└──────────────────────┼───────────────────────────────────────┘
                       │
                       │ HTTPS
                       │
┌──────────────────────▼───────────────────────────────────────┐
│                   VERCEL EDGE NETWORK                        │
│                                                               │
│  ┌────────────────────────────────────────────────┐         │
│  │          Next.js App (App Router)              │         │
│  │                                                 │         │
│  │  ┌──────────────┐      ┌──────────────┐       │         │
│  │  │ Pages (SSR)  │      │  API Routes  │       │         │
│  │  │              │      │              │       │         │
│  │  │ - Dashboard  │      │ - /api/auth  │       │         │
│  │  │ - Creator    │      │ - /api/streams│      │         │
│  │  │ - Search     │      │ - /api/creators│     │         │
│  │  └──────────────┘      └──────────────┘       │         │
│  └────────────────────────────────────────────────┘         │
└──────────────────────┬───────────────────────────────────────┘
                       │
        ┌──────────────┼──────────────┐
        │              │              │
        ▼              ▼              ▼
┌──────────────┐ ┌──────────┐ ┌─────────────┐
│   SUPABASE   │ │  RESEND  │ │ EXTERNAL    │
│              │ │          │ │ APIs        │
│ ┌──────────┐ │ │ Email    │ │             │
│ │PostgreSQL│ │ │ Service  │ │ - YouTube   │
│ └──────────┘ │ │          │ │ - Twitch    │
│              │ │          │ │ - Twitter   │
│ ┌──────────┐ │ └──────────┘ └─────────────┘
│ │   Auth   │ │
│ │ (OAuth)  │ │
│ └──────────┘ │
│              │
│ ┌──────────┐ │
│ │ Realtime │ │
│ └──────────┘ │
└──────────────┘
```

---

### Data Flow Examples

**Example 1: Creator Creates Schedule**
```
1. User submits schedule form → POST /api/streams/create
2. API validates inputs (date, URL format)
3. API verifies stream URL via YouTube/Twitch API
4. If valid → Insert into Supabase `streams` table
5. Trigger notification job for followers
6. Return success → UI shows confirmation
7. Supabase Realtime broadcasts new stream to followers' dashboards
```

**Example 2: Consumer Follows Creator**
```
1. User searches "VtuberName" → GET /api/creators/search?q=VtuberName
2. API queries YouTube Data API (channels.list)
3. API queries Twitch Helix API (users endpoint)
4. Return results → UI displays creator profiles
5. User clicks "Follow" → POST /api/creators/follow
6. Insert into `follows` table (user_id, creator_id)
7. Return success → UI updates button to "Following"
```

**Example 3: Notification System**
```
1. Vercel Cron runs every 15 minutes → /api/cron/send-notifications
2. Query streams starting in next 30 min (user's pref)
3. Get all users following those creators with notifications enabled
4. For each user:
   - If email enabled → Send via Resend
   - If push enabled → Send via Web Push API
5. Mark notification as sent in `notifications` table
6. Log results
```

---

## Database Schema

### Supabase PostgreSQL Tables

**Technical Notes:**
- Use UUID for all primary keys
- Enable Row Level Security (RLS) on all tables
- Use Supabase's `auth.users` table for user authentication
- Timestamps: `created_at`, `updated_at` (auto-managed by triggers)

---

### Table: `profiles`
Extends Supabase `auth.users` with application-specific data.

```sql
CREATE TABLE profiles (
  id UUID PRIMARY KEY REFERENCES auth.users(id) ON DELETE CASCADE,
  username VARCHAR(50) UNIQUE NOT NULL,
  display_name VARCHAR(100),
  avatar_url TEXT,
  bio TEXT,
  timezone VARCHAR(50) DEFAULT 'UTC',
  is_creator BOOLEAN DEFAULT FALSE,
  -- Notification preferences
  notifications_enabled BOOLEAN DEFAULT FALSE,
  email_notifications BOOLEAN DEFAULT FALSE,
  push_notifications BOOLEAN DEFAULT FALSE,
  notification_timing VARCHAR(20) DEFAULT '30min', -- '30min', '1hour', '1day', 'at_start'
  -- OAuth metadata
  youtube_channel_id VARCHAR(50),
  youtube_channel_url TEXT,
  twitch_username VARCHAR(50),
  twitch_user_id VARCHAR(50),
  twitter_handle VARCHAR(50),
  -- Timestamps
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW()
);

-- Indexes
CREATE INDEX idx_profiles_username ON profiles(username);
CREATE INDEX idx_profiles_is_creator ON profiles(is_creator);
CREATE INDEX idx_profiles_youtube_channel_id ON profiles(youtube_channel_id);
CREATE INDEX idx_profiles_twitch_user_id ON profiles(twitch_user_id);
```

---

### Table: `creators`
Creator-specific information and settings.

```sql
CREATE TABLE creators (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID NOT NULL REFERENCES profiles(id) ON DELETE CASCADE,
  slug VARCHAR(50) UNIQUE NOT NULL, -- URL-friendly username for public pages
  bio TEXT,
  banner_url TEXT,
  -- Platform links
  youtube_url TEXT,
  twitch_url TEXT,
  twitter_url TEXT,
  -- Settings
  auto_publish BOOLEAN DEFAULT TRUE, -- Auto-publish schedules or require manual approval
  -- Stats (cached)
  follower_count INTEGER DEFAULT 0,
  total_streams INTEGER DEFAULT 0,
  -- Timestamps
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW()
);

-- Indexes
CREATE INDEX idx_creators_user_id ON creators(user_id);
CREATE INDEX idx_creators_slug ON creators(slug);
```

---

### Table: `streams`
Scheduled livestream entries.

```sql
CREATE TABLE streams (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  creator_id UUID NOT NULL REFERENCES creators(id) ON DELETE CASCADE,
  -- Stream details
  title VARCHAR(200) NOT NULL,
  description TEXT,
  thumbnail_url TEXT,
  -- Scheduling
  scheduled_start TIMESTAMPTZ NOT NULL,
  scheduled_end TIMESTAMPTZ, -- Optional
  timezone VARCHAR(50) DEFAULT 'UTC',
  -- Platforms (can be multiple)
  platforms TEXT[] NOT NULL, -- ['youtube', 'twitch']
  youtube_url TEXT,
  youtube_video_id VARCHAR(20),
  twitch_url TEXT,
  twitch_channel VARCHAR(50),
  -- Verification
  verified BOOLEAN DEFAULT FALSE,
  verification_attempted_at TIMESTAMPTZ,
  verification_error TEXT,
  -- Status
  status VARCHAR(20) DEFAULT 'draft', -- 'draft', 'published', 'live', 'completed', 'cancelled'
  published_at TIMESTAMPTZ,
  -- Metadata
  tags TEXT[],
  -- Timestamps
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW()
);

-- Indexes
CREATE INDEX idx_streams_creator_id ON streams(creator_id);
CREATE INDEX idx_streams_scheduled_start ON streams(scheduled_start);
CREATE INDEX idx_streams_status ON streams(status);
CREATE INDEX idx_streams_platforms ON streams USING GIN(platforms);
CREATE INDEX idx_streams_published_scheduled ON streams(status, scheduled_start) WHERE status = 'published';
```

---

### Table: `follows`
User following relationships.

```sql
CREATE TABLE follows (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID NOT NULL REFERENCES profiles(id) ON DELETE CASCADE,
  creator_id UUID NOT NULL REFERENCES creators(id) ON DELETE CASCADE,
  -- Preferences
  notifications_enabled BOOLEAN DEFAULT TRUE, -- Override user's global notification setting
  -- Timestamps
  created_at TIMESTAMPTZ DEFAULT NOW(),
  
  UNIQUE(user_id, creator_id)
);

-- Indexes
CREATE INDEX idx_follows_user_id ON follows(user_id);
CREATE INDEX idx_follows_creator_id ON follows(creator_id);
```

---

### Table: `edit_suggestions`
Community edit suggestions for streams.

```sql
CREATE TABLE edit_suggestions (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  stream_id UUID NOT NULL REFERENCES streams(id) ON DELETE CASCADE,
  suggested_by UUID NOT NULL REFERENCES profiles(id) ON DELETE CASCADE,
  -- Proposed changes (JSON object with field: value pairs)
  suggested_changes JSONB NOT NULL,
  reason TEXT NOT NULL,
  -- Status
  status VARCHAR(20) DEFAULT 'pending', -- 'pending', 'approved', 'rejected'
  reviewed_by UUID REFERENCES profiles(id),
  reviewed_at TIMESTAMPTZ,
  -- Timestamps
  created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Indexes
CREATE INDEX idx_edit_suggestions_stream_id ON edit_suggestions(stream_id);
CREATE INDEX idx_edit_suggestions_status ON edit_suggestions(status);
```

---

### Table: `notifications`
Notification history and tracking.

```sql
CREATE TABLE notifications (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID NOT NULL REFERENCES profiles(id) ON DELETE CASCADE,
  stream_id UUID REFERENCES streams(id) ON DELETE SET NULL,
  -- Notification details
  type VARCHAR(50) NOT NULL, -- 'stream_reminder', 'schedule_change', 'new_stream'
  channel VARCHAR(20) NOT NULL, -- 'email', 'push'
  -- Status
  status VARCHAR(20) DEFAULT 'pending', -- 'pending', 'sent', 'failed'
  sent_at TIMESTAMPTZ,
  error TEXT,
  -- Timestamps
  created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Indexes
CREATE INDEX idx_notifications_user_id ON notifications(user_id);
CREATE INDEX idx_notifications_status ON notifications(status);
CREATE INDEX idx_notifications_stream_id ON notifications(stream_id);
```

---

### Table: `platform_cache`
Cache for external API responses to reduce API calls.

```sql
CREATE TABLE platform_cache (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  platform VARCHAR(20) NOT NULL, -- 'youtube', 'twitch'
  cache_key VARCHAR(200) NOT NULL, -- e.g., 'channel:UC1234567890'
  cache_data JSONB NOT NULL,
  expires_at TIMESTAMPTZ NOT NULL,
  created_at TIMESTAMPTZ DEFAULT NOW(),
  
  UNIQUE(platform, cache_key)
);

-- Indexes
CREATE INDEX idx_platform_cache_key ON platform_cache(platform, cache_key);
CREATE INDEX idx_platform_cache_expires ON platform_cache(expires_at);
```

---

### Row Level Security (RLS) Policies

**Enable RLS on all tables:**
```sql
ALTER TABLE profiles ENABLE ROW LEVEL SECURITY;
ALTER TABLE creators ENABLE ROW LEVEL SECURITY;
ALTER TABLE streams ENABLE ROW LEVEL SECURITY;
ALTER TABLE follows ENABLE ROW LEVEL SECURITY;
ALTER TABLE edit_suggestions ENABLE ROW LEVEL SECURITY;
ALTER TABLE notifications ENABLE ROW LEVEL SECURITY;
```

**Example policies for `streams` table:**
```sql
-- Public read for published streams
CREATE POLICY "Published streams are viewable by everyone" ON streams
  FOR SELECT USING (status = 'published');

-- Creators can view their own drafts
CREATE POLICY "Creators can view own streams" ON streams
  FOR SELECT USING (creator_id IN (
    SELECT id FROM creators WHERE user_id = auth.uid()
  ));

-- Creators can insert/update/delete their own streams
CREATE POLICY "Creators can manage own streams" ON streams
  FOR ALL USING (creator_id IN (
    SELECT id FROM creators WHERE user_id = auth.uid()
  ));
```

---

## API Specifications

### Authentication Endpoints

**POST /api/auth/callback**
- Handle OAuth callback from Supabase Auth
- Create/update user profile
- Redirect to dashboard

---

### Stream Management Endpoints

**POST /api/streams/create**
- **Auth:** Required (Creator)
- **Body:**
  ```json
  {
    "title": "string",
    "description": "string",
    "scheduled_start": "ISO 8601 datetime",
    "timezone": "string",
    "platforms": ["youtube", "twitch"],
    "youtube_url": "string (optional)",
    "twitch_url": "string (optional)",
    "auto_publish": boolean,
    "tags": ["string"]
  }
  ```
- **Response:**
  ```json
  {
    "success": true,
    "stream": { /* stream object */ },
    "verification": {
      "youtube": { "valid": true },
      "twitch": { "valid": true }
    }
  }
  ```

**GET /api/streams?creator_id={id}&status={status}&limit={n}**
- **Auth:** Optional (public for published, required for drafts)
- **Query Params:**
  - `creator_id` (UUID): Filter by creator
  - `status` (string): Filter by status
  - `from_date` (ISO 8601): Start date filter
  - `to_date` (ISO 8601): End date filter
  - `limit` (number): Max results (default 50)
- **Response:**
  ```json
  {
    "streams": [
      {
        "id": "uuid",
        "title": "string",
        "creator": { /* creator object */ },
        "scheduled_start": "ISO 8601",
        "platforms": ["youtube"],
        "youtube_url": "string"
      }
    ],
    "total": 100
  }
  ```

**PATCH /api/streams/{id}**
- **Auth:** Required (Creator owns stream)
- **Body:** Partial stream object
- **Response:** Updated stream object

**DELETE /api/streams/{id}**
- **Auth:** Required (Creator owns stream)
- **Response:** `{ "success": true }`

---

### Creator Endpoints

**GET /api/creators/search?q={query}&platform={platform}**
- **Auth:** Optional
- **Query Params:**
  - `q` (string): Search term (username/channel name)
  - `platform` (string): 'youtube' | 'twitch' | 'all'
- **Response:**
  ```json
  {
    "results": [
      {
        "id": "uuid",
        "username": "string",
        "display_name": "string",
        "avatar_url": "string",
        "platforms": {
          "youtube": { "channel_id": "string", "url": "string" },
          "twitch": { "username": "string", "url": "string" }
        },
        "follower_count": 123,
        "is_followed": false
      }
    ]
  }
  ```

**POST /api/creators/follow**
- **Auth:** Required
- **Body:**
  ```json
  {
    "creator_id": "uuid",
    "notifications_enabled": true
  }
  ```
- **Response:** `{ "success": true }`

**DELETE /api/creators/follow/{creator_id}**
- **Auth:** Required
- **Response:** `{ "success": true }`

**GET /api/creators/{slug}**
- **Auth:** Optional
- **Response:** Creator profile with upcoming streams

---

### Edit Suggestion Endpoints

**POST /api/suggestions/create**
- **Auth:** Required
- **Body:**
  ```json
  {
    "stream_id": "uuid",
    "suggested_changes": {
      "title": "Corrected Title",
      "scheduled_start": "2025-12-01T19:00:00Z"
    },
    "reason": "Creator posted wrong time on Twitter"
  }
  ```
- **Response:** Suggestion object

**GET /api/suggestions?stream_id={id}&status={status}**
- **Auth:** Required (Creator for their streams)
- **Response:** List of suggestions

**PATCH /api/suggestions/{id}/approve**
- **Auth:** Required (Creator owns stream)
- **Response:** Updated stream with approved changes

**PATCH /api/suggestions/{id}/reject**
- **Auth:** Required (Creator owns stream)
- **Response:** `{ "success": true }`

---

### User Feed Endpoint

**GET /api/feed**
- **Auth:** Required
- **Query Params:**
  - `from_date` (ISO 8601)
  - `to_date` (ISO 8601)
  - `platform` (string): Filter by platform
  - `creator_id` (UUID): Filter by specific creator
- **Response:**
  ```json
  {
    "streams": [
      {
        "id": "uuid",
        "title": "string",
        "creator": { /* creator info */ },
        "scheduled_start": "ISO 8601",
        "scheduled_start_local": "ISO 8601", // User's timezone
        "platforms": ["youtube"],
        "youtube_url": "string"
      }
    ],
    "user_timezone": "America/Los_Angeles"
  }
  ```

---

### Notification Endpoints

**POST /api/notifications/subscribe**
- **Auth:** Required
- **Body:**
  ```json
  {
    "type": "push",
    "subscription": { /* Web Push subscription object */ }
  }
  ```
- **Response:** `{ "success": true }`

**PATCH /api/notifications/settings**
- **Auth:** Required
- **Body:**
  ```json
  {
    "notifications_enabled": true,
    "email_notifications": true,
    "push_notifications": true,
    "notification_timing": "30min"
  }
  ```
- **Response:** Updated user profile

---

### Cron/Background Job Endpoints

**POST /api/cron/send-notifications**
- **Auth:** Vercel Cron secret header
- **Description:** Runs every 15 minutes to send stream reminders
- **Logic:**
  1. Get all streams starting in next 30 min, 1 hour, 1 day (based on user prefs)
  2. Get users following those creators with notifications enabled
  3. Check if notification already sent (dedupe)
  4. Send email via Resend or push via Web Push API
  5. Log in `notifications` table

**POST /api/cron/verify-streams**
- **Auth:** Vercel Cron secret header
- **Description:** Runs daily to re-verify stream URLs
- **Logic:**
  1. Get all published streams scheduled in next 7 days
  2. Re-verify YouTube/Twitch URLs via API
  3. Update `verified` status
  4. If verification fails, mark as unverified and notify creator

---

## Authentication & Authorization

### OAuth Flow (Supabase Auth)

**Supported Providers:**
- Google (for YouTube access)
- Twitch
- Twitter/X

**Flow:**
```
1. User clicks "Sign in with Google"
2. Frontend calls Supabase Auth: supabase.auth.signInWithOAuth({ provider: 'google' })
3. Supabase redirects to Google OAuth
4. User authorizes
5. Google redirects back to Supabase with authorization code
6. Supabase exchanges code for tokens, creates session
7. Supabase redirects to app callback URL: /auth/callback
8. App creates/updates user profile in `profiles` table
9. App redirects to dashboard
```

**Storing OAuth Tokens:**
- Supabase automatically stores OAuth tokens in encrypted format
- Access via: `supabase.auth.session()?.provider_token`
- Use tokens to call YouTube/Twitch APIs on behalf of user

---

### Role-Based Access Control (RBAC)

**Roles:**
- **Consumer:** Default role, can follow creators and view schedules
- **Creator:** Has `is_creator = true` in profiles, can manage streams

**Permission Logic:**
- Use Supabase RLS policies to enforce permissions at database level
- API endpoints check `auth.uid()` against resource ownership
- Frontend hides/shows UI elements based on user role

**Example:** Only creators can access `/dashboard/streams/new`
```typescript
// middleware.ts or page-level auth check
const { data: profile } = await supabase
  .from('profiles')
  .select('is_creator')
  .eq('id', user.id)
  .single();

if (!profile.is_creator) {
  redirect('/dashboard'); // or show error
}
```

---

## Service Setup Instructions

### 1. Vercel Setup

**Prerequisites:**
- GitHub account with repository for this project
- Vercel account (free tier sufficient for MVP)

**Steps:**

1. **Create Vercel Project:**
   - Go to [vercel.com](https://vercel.com)
   - Click "Add New..." → "Project"
   - Import your GitHub repository
   - Framework Preset: Next.js
   - Root Directory: `./` (or your Next.js app folder)

2. **Environment Variables:**
   In Vercel project settings → Environment Variables, add:
   ```bash
   # Supabase
   NEXT_PUBLIC_SUPABASE_URL=https://your-project.supabase.co
   NEXT_PUBLIC_SUPABASE_ANON_KEY=your-anon-key
   SUPABASE_SERVICE_ROLE_KEY=your-service-role-key
   
   # External APIs
   YOUTUBE_API_KEY=your-youtube-api-key
   TWITCH_CLIENT_ID=your-twitch-client-id
   TWITCH_CLIENT_SECRET=your-twitch-client-secret
   TWITTER_BEARER_TOKEN=your-twitter-bearer-token
   
   # Resend
   RESEND_API_KEY=your-resend-api-key
   
   # App Config
   NEXT_PUBLIC_APP_URL=https://your-app.vercel.app
   
   # Cron Job Secret (generate random string)
   CRON_SECRET=your-random-secret
   ```

3. **Deploy:**
   - Push to `main` branch → auto-deploys to production
   - Push to other branches → preview deployments

4. **Setup Cron Jobs:**
   Create `vercel.json` in project root:
   ```json
   {
     "crons": [
       {
         "path": "/api/cron/send-notifications",
         "schedule": "*/15 * * * *"
       },
       {
         "path": "/api/cron/verify-streams",
         "schedule": "0 2 * * *"
       }
     ]
   }
   ```

5. **Configure Custom Domain (Optional):**
   - Vercel Project Settings → Domains
   - Add your domain and follow DNS instructions

---

### 2. Supabase Setup

**Prerequisites:**
- Supabase account (free tier: 500MB database, 50K monthly active users)

**Steps:**

1. **Create Supabase Project:**
   - Go to [supabase.com](https://supabase.com)
   - Click "New Project"
   - Organization: Create or select existing
   - Name: `vtuber-scheduler` (or your choice)
   - Database Password: Generate strong password (save securely)
   - Region: Choose closest to users (e.g., `us-west-1`)
   - Pricing Plan: Free (sufficient for MVP)

2. **Get API Keys:**
   - Project Settings → API
   - Copy:
     - `Project URL` → `NEXT_PUBLIC_SUPABASE_URL`
     - `anon public` key → `NEXT_PUBLIC_SUPABASE_ANON_KEY`
     - `service_role` key → `SUPABASE_SERVICE_ROLE_KEY` (keep secret!)

3. **Configure Authentication:**
   - Authentication → Providers
   - Enable providers:
     - **Google:**
       - Get credentials from [Google Cloud Console](https://console.cloud.google.com)
       - Create OAuth 2.0 Client ID
       - Add redirect URI: `https://your-project.supabase.co/auth/v1/callback`
       - Scopes: `email`, `profile`, `https://www.googleapis.com/auth/youtube.readonly`
     - **Twitch:**
       - Register app at [dev.twitch.tv](https://dev.twitch.tv/console/apps)
       - OAuth Redirect URLs: `https://your-project.supabase.co/auth/v1/callback`
       - Get Client ID and Secret
     - **Twitter:**
       - Create app at [developer.twitter.com](https://developer.twitter.com/en/portal/dashboard)
       - Enable OAuth 2.0
       - Callback URL: `https://your-project.supabase.co/auth/v1/callback`

4. **Setup Database Schema:**
   - SQL Editor → New Query
   - Copy and run the SQL from the Database Schema section above
   - Run table creation scripts in order
   - Run RLS policy scripts

5. **Enable Realtime (Optional):**
   - Database → Replication
   - Enable replication for `streams` table
   - Enables real-time subscriptions for live updates

6. **Configure Email Templates:**
   - Authentication → Email Templates
   - Customize confirmation, reset password emails
   - Use Resend SMTP (see Resend setup)

---

### 3. Resend Setup

**Prerequisites:**
- Resend account (free tier: 100 emails/day, 1 domain)
- Custom domain (optional but recommended for production)

**Steps:**

1. **Create Resend Account:**
   - Go to [resend.com](https://resend.com)
   - Sign up with email

2. **Get API Key:**
   - Dashboard → API Keys
   - Create API Key
   - Copy key → `RESEND_API_KEY` environment variable

3. **Add Domain (Optional for Production):**
   - Dashboard → Domains
   - Add your domain (e.g., `notifications.yourapp.com`)
   - Add DNS records (MX, TXT) to your domain provider
   - Verify domain

4. **Setup Email Templates:**
   Create reusable email templates in your Next.js app:

   **Example: Stream Reminder Email**
   ```typescript
   // lib/emails/stream-reminder.tsx
   import { Html, Head, Body, Container, Heading, Text, Button } from '@react-email/components';
   
   interface StreamReminderEmailProps {
     userName: string;
     creatorName: string;
     streamTitle: string;
     streamTime: string;
     streamUrl: string;
   }
   
   export default function StreamReminderEmail({
     userName,
     creatorName,
     streamTitle,
     streamTime,
     streamUrl
   }: StreamReminderEmailProps) {
     return (
       <Html>
         <Head />
         <Body style={{ fontFamily: 'sans-serif' }}>
           <Container>
             <Heading>Stream Starting Soon!</Heading>
             <Text>Hi {userName},</Text>
             <Text>
               {creatorName} is going live in 30 minutes with: <strong>{streamTitle}</strong>
             </Text>
             <Text>Scheduled Time: {streamTime}</Text>
             <Button href={streamUrl}>Watch Now</Button>
           </Container>
         </Body>
       </Html>
     );
   }
   ```

5. **Send Email via API:**
   ```typescript
   // app/api/cron/send-notifications/route.ts
   import { Resend } from 'resend';
   import StreamReminderEmail from '@/lib/emails/stream-reminder';
   
   const resend = new Resend(process.env.RESEND_API_KEY);
   
   async function sendStreamReminder(user, stream) {
     const { data, error } = await resend.emails.send({
       from: 'VTuber Scheduler <notifications@yourapp.com>',
       to: user.email,
       subject: `${stream.creator.name} is going live soon!`,
       react: StreamReminderEmail({
         userName: user.display_name,
         creatorName: stream.creator.name,
         streamTitle: stream.title,
         streamTime: formatTime(stream.scheduled_start, user.timezone),
         streamUrl: stream.youtube_url || stream.twitch_url
       })
     });
     
     if (error) {
       console.error('Email send failed:', error);
     }
     return data;
   }
   ```

6. **Monitor Email Deliverability:**
   - Resend Dashboard → Emails
   - View sent, delivered, bounced, complained
   - Check spam reports and adjust content if needed

---

### 4. External API Setup

**YouTube Data API v3:**

1. **Google Cloud Console:**
   - Go to [console.cloud.google.com](https://console.cloud.google.com)
   - Create new project or select existing
   - Enable "YouTube Data API v3"
   - Credentials → Create Credentials → API Key
   - Copy API Key → `YOUTUBE_API_KEY`

2. **Usage:**
   ```typescript
   // lib/youtube.ts
   const YOUTUBE_API_KEY = process.env.YOUTUBE_API_KEY;
   const YOUTUBE_API_URL = 'https://www.googleapis.com/youtube/v3';
   
   export async function verifyYouTubeVideo(videoId: string) {
     const response = await fetch(
       `${YOUTUBE_API_URL}/videos?part=snippet,liveStreamingDetails&id=${videoId}&key=${YOUTUBE_API_KEY}`
     );
     const data = await response.json();
     return data.items?.[0] || null;
   }
   
   export async function searchYouTubeChannel(query: string) {
     const response = await fetch(
       `${YOUTUBE_API_URL}/search?part=snippet&type=channel&q=${encodeURIComponent(query)}&key=${YOUTUBE_API_KEY}`
     );
     const data = await response.json();
     return data.items || [];
   }
   ```

**Twitch Helix API:**

1. **Twitch Developer Console:**
   - Go to [dev.twitch.tv/console](https://dev.twitch.tv/console)
   - Register application
   - OAuth Redirect URLs: `http://localhost:3000/api/auth/callback` (dev), `https://yourapp.com/api/auth/callback` (prod)
   - Copy Client ID and Client Secret

2. **Get App Access Token:**
   ```typescript
   // lib/twitch.ts
   const TWITCH_CLIENT_ID = process.env.TWITCH_CLIENT_ID;
   const TWITCH_CLIENT_SECRET = process.env.TWITCH_CLIENT_SECRET;
   
   let accessToken: string | null = null;
   
   async function getTwitchAccessToken() {
     if (accessToken) return accessToken;
     
     const response = await fetch('https://id.twitch.tv/oauth2/token', {
       method: 'POST',
       headers: { 'Content-Type': 'application/x-www-form-urlencoded' },
       body: new URLSearchParams({
         client_id: TWITCH_CLIENT_ID,
         client_secret: TWITCH_CLIENT_SECRET,
         grant_type: 'client_credentials'
       })
     });
     const data = await response.json();
     accessToken = data.access_token;
     return accessToken;
   }
   
   export async function getTwitchUser(username: string) {
     const token = await getTwitchAccessToken();
     const response = await fetch(
       `https://api.twitch.tv/helix/users?login=${username}`,
       {
         headers: {
           'Client-ID': TWITCH_CLIENT_ID,
           'Authorization': `Bearer ${token}`
         }
       }
     );
     const data = await response.json();
     return data.data?.[0] || null;
   }
   ```

---

### 5. Web Push Notifications Setup

**Steps:**

1. **Generate VAPID Keys:**
   ```bash
   npx web-push generate-vapid-keys
   ```
   Add to environment variables:
   ```bash
   NEXT_PUBLIC_VAPID_PUBLIC_KEY=your-public-key
   VAPID_PRIVATE_KEY=your-private-key
   ```

2. **Client-Side Subscription:**
   ```typescript
   // lib/push-notifications.ts
   export async function subscribeToPushNotifications() {
     if (!('serviceWorker' in navigator) || !('PushManager' in window)) {
       throw new Error('Push notifications not supported');
     }
     