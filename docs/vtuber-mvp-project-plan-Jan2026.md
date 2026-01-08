# VTuber Scheduling Platform - MVP Project Plan

**Project Manager:** [Your Name]  
**Project Start Date:** January 2026  
**Target MVP Launch:** April 2026 (12-14 weeks)  
**Team Size:** 1-3 part-time contributors  
**Weekly Commitment:** 10-15 hours/person  

---

## Executive Summary

This project plan outlines the development roadmap for the VTuber Scheduling Platform MVP. The plan assumes:
- **Services pre-configured:** Vercel, Supabase, and Resend accounts are set up
- **Part-time resources:** Contributors working 10-15 hours per week
- **AI-assisted development:** Heavy use of Claude and other AI coding tools
- **Non-technical friendly:** Each phase includes detailed instructions suitable for beginners

**Total Estimated Time:** 12-14 weeks (120-140 hours total per contributor)

---

## Table of Contents

1. [Project Phases Overview](#project-phases-overview)
2. [Phase 0: Project Setup & Foundation](#phase-0-project-setup--foundation)
3. [Phase 1: User Authentication](#phase-1-user-authentication)
4. [Phase 2: Basic Database & User Profiles](#phase-2-basic-database--user-profiles)
5. [Phase 3: Creator Features](#phase-3-creator-features)
6. [Phase 4: Consumer Features](#phase-4-consumer-features)
7. [Phase 5: Notifications System](#phase-5-notifications-system)
8. [Phase 6: Polish & Testing](#phase-6-polish--testing)
9. [Phase 7: Deployment & Launch](#phase-7-deployment--launch)
10. [Resources & Templates](#resources--templates)
11. [Risk Management](#risk-management)
12. [Success Criteria](#success-criteria)

---

## Project Phases Overview

| Phase | Description | Duration | Hours |
|-------|-------------|----------|-------|
| **Phase 0** | Project Setup & Foundation | 1 week | 10-12h |
| **Phase 1** | User Authentication | 1-2 weeks | 15-20h |
| **Phase 2** | Database & User Profiles | 1-2 weeks | 15-20h |
| **Phase 3** | Creator Features | 2-3 weeks | 30-40h |
| **Phase 4** | Consumer Features | 2-3 weeks | 25-35h |
| **Phase 5** | Notifications System | 1-2 weeks | 15-20h |
| **Phase 6** | Polish & Testing | 1-2 weeks | 15-20h |
| **Phase 7** | Deployment & Launch | 1 week | 10-15h |
| **TOTAL** | | **12-14 weeks** | **135-182h** |

---

## Phase 0: Project Setup & Foundation

**Duration:** 1 week (10-12 hours)  
**Goal:** Set up development environment and project structure  
**Team:** All contributors

### Deliverables
- [ ] Creator dashboard
- [ ] Stream creation form
- [ ] Stream editing/deletion
- [ ] YouTube/Twitch URL verification
- [ ] Multi-platform stream support
- [ ] Draft/publish workflow
- [ ] Creator public page

### Tasks

#### Task 3.1: Create Creator Dashboard (4 hours)

**What you're doing:** Building the main page creators see when they log in.

**Instructions:**

**AI Prompt:**
```
Create a creator dashboard for my VTuber scheduling app.

File: app/(dashboard)/creator/page.tsx

Dashboard should show:
1. Welcome message with creator name
2. Quick stats cards:
   - Total upcoming streams
   - Total followers
   - Streams this week
3. List of upcoming streams (next 10)
   - Each showing: title, platform icons, date/time, status
   - Actions: Edit, Delete, View Public Page
4. "Create New Stream" prominent button
5. Pending edit suggestions count (if any)
6. Filter options: All, Drafts, Published, Past

Requirements:
- Fetch data from Supabase
- Use Server Component for initial load
- Mobile-responsive grid layout
- Loading skeletons
- Empty states with call-to-action
- Tailwind CSS styling

Provide complete code with TypeScript types.
```

#### Task 3.2: Build Stream Creation Form (5 hours)

**What you're doing:** The form creators use to schedule new streams.

**Instructions:**

**AI Prompt:**
```
Create a comprehensive stream creation form.

File: app/(dashboard)/creator/streams/new/page.tsx

Form fields:
1. Stream Title (required, 200 char max)
2. Description (optional, rich text, 2000 char max)
3. Scheduled Start Date & Time (required)
   - Date picker
   - Time picker
   - Shows creator's timezone
4. Scheduled End Time (optional)
5. Platform Selection (checkboxes)
   - YouTube (checkbox + URL input)
   - Twitch (checkbox + URL input)
   - Must select at least one
6. Stream URLs (shown based on platform selection)
   - YouTube URL (validate format)
   - Twitch URL (validate format)
   - Verify button for each
7. Thumbnail URL (optional)
8. Tags (optional, multi-select)
9. Publishing options:
   - Save as Draft
   - Publish Now

Requirements:
- Use React Hook Form + Zod validation
- Real-time URL validation
- Verify stream exists via API before saving
- Show verification status (verified/pending/failed)
- Multi-step form or single page (your choice)
- Auto-save drafts (optional)
- Mobile-responsive
- Clear error messages

Provide complete code.

I have these packages: react-hook-form, zod, date-fns
```

#### Task 3.3: Implement URL Verification (4 hours)

**What you're doing:** Checking if YouTube/Twitch links are real and valid.

**Instructions:**

**AI Prompt:**
```
Create YouTube and Twitch URL verification system.

Files needed:
1. lib/api/youtube.ts - YouTube verification functions
2. lib/api/twitch.ts - Twitch verification functions
3. app/api/verify-stream/route.ts - API endpoint

YouTube verification should:
- Extract video ID from URL (handle multiple formats)
- Call YouTube Data API to verify video exists
- Check if it's a livestream
- Return: valid (bool), title, thumbnail, scheduled time

Twitch verification should:
- Extract channel name from URL
- Call Twitch API to verify channel exists
- Return: valid (bool), channel name, avatar, is_live

API endpoint should:
- Accept POST request with { platform, url }
- Call appropriate verification function
- Cache results to reduce API calls
- Return verification status

Requirements:
- Handle API rate limits
- Use environment variables for API keys
- Proper error handling
- TypeScript types
- Cache in 'platform_cache' table (1 hour expiry)

Provide all three files with complete code.

My API credentials are in environment variables:
- YOUTUBE_API_KEY
- TWITCH_CLIENT_ID
- TWITCH_CLIENT_SECRET
```

#### Task 3.4: Create Server Actions for Stream Management (3 hours)

**What you're doing:** Backend functions to save, update, and delete streams.

**Instructions:**

**AI Prompt:**
```
Create Server Actions for stream management.

File: app/actions/streams.ts

Functions needed:
1. createStream(creatorId, streamData)
   - Validates all fields
   - Inserts into 'streams' table
   - Returns stream ID or error

2. updateStream(streamId, streamData)
   - Verifies creator owns stream
   - Updates stream fields
   - Maintains change history
   - Returns updated stream or error

3. deleteStream(streamId)
   - Verifies creator owns stream
   - Soft delete (set status to 'deleted')
   - Returns success or error

4. publishStream(streamId)
   - Changes status from 'draft' to 'published'
   - Sets published_at timestamp
   - Triggers notifications (Phase 5)

5. getCreatorStreams(creatorId, filters)
   - Returns streams with filtering
   - Supports: status, date range, platforms

Requirements:
- Use "use server" directive
- Validate with Zod schemas
- Check creator ownership
- Proper error messages
- TypeScript types
- Transaction support for complex operations

Provide complete file with all functions and schemas.
```

#### Task 3.5: Build Stream Edit Page (3 hours)

**What you're doing:** Letting creators modify existing streams.

**Instructions:**

**AI Prompt:**
```
Create a stream editing page.

File: app/(dashboard)/creator/streams/[streamId]/edit/page.tsx

Requirements:
- Load existing stream data
- Use same form as creation (reuse component)
- Pre-fill all fields with current values
- Show change history (who edited, when)
- "Save Changes" button
- "Delete Stream" button (with confirmation)
- "Cancel" button (back to dashboard)
- Verification status indicator
- Re-verify URLs option

Additional features:
- Show pending edit suggestions from community
- Highlight changed fields
- Prevent editing if stream already ended
- Warning if stream is in progress

Provide:
1. Edit page component
2. Reusable form component (if splitting from creation form)
3. Stream loading and error handling

Complete code with TypeScript.
```

#### Task 3.6: Implement Multi-Platform Support (2 hours)

**What you're doing:** Handling streams that are on multiple platforms simultaneously.

**Instructions:**

**AI Prompt:**
```
Enhance stream forms to properly handle multi-platform streams.

Requirements:
1. Store multiple URLs in single stream record
2. Show platform badges in UI (YouTube icon, Twitch icon)
3. Verify each URL independently
4. Display verification status for each platform
5. Handle case where one verifies but another fails
6. Allow removing a platform from multi-platform stream

Update these files:
- Stream creation form
- Stream display components
- Database queries to handle TEXT[] platforms field

Provide:
1. Updated form component with multi-platform UI
2. Helper functions for platform handling
3. Display component for platform badges

Complete code for all updates needed.
```

#### Task 3.7: Create Public Creator Page (4 hours)

**What you're doing:** Public page anyone can view showing creator's schedule.

**Instructions:**

**AI Prompt:**
```
Create a public creator profile page.

File: app/creator/[username]/page.tsx

Page should display:
1. Creator banner image (if set)
2. Creator avatar and name
3. Bio text
4. Platform links (YouTube, Twitch, Twitter) as badges/buttons
5. Follower count
6. "Follow" button (if not logged in, prompt to sign in)
7. List of upcoming streams (next 30 days):
   - Stream title
   - Date/time (in viewer's timezone)
   - Platform badges
   - Countdown timer if < 24 hours
   - Thumbnail (if available)
   - Link to external stream
8. Past streams section (collapsed by default)

Requirements:
- Server-side rendered (SSR) for SEO
- Public route (no auth required)
- Mobile-responsive
- Shareable URL: /creator/username or /c/username
- Open Graph meta tags for social sharing
- Canonical URL
- 404 if creator not found
- Loading state

Provide complete component code.
```

#### Task 3.8: Add Stream Draft/Publish Workflow (2 hours)

**What you're doing:** Letting creators save drafts before publishing.

**Instructions:**

**AI Prompt:**
```
Implement draft and publish workflow for streams.

Features needed:
1. Save stream as draft (status: 'draft')
   - Not visible to public
   - Not in followers' feeds
   - Shows in creator's dashboard with "Draft" badge

2. Publish stream (status: 'published')
   - Visible on public page
   - Appears in followers' feeds
   - Triggers notifications
   - Sets published_at timestamp

3. Auto-publish option
   - Checkbox on creation form
   - If checked, publish immediately after save
   - If unchecked, save as draft

4. Bulk actions
   - Publish multiple drafts at once
   - Delete multiple drafts

Update:
- Stream creation form (add auto-publish toggle)
- Creator dashboard (show draft count, publish action)
- Server actions (handle status transitions)

Provide all updated code.
```

#### Task 3.9: Add Stream Statistics (Optional, 2 hours)

**What you're doing:** Showing creators basic analytics.

**Instructions:**

**AI Prompt:**
```
Create a statistics view for creators.

File: app/(dashboard)/creator/stats/page.tsx

Statistics to show:
1. Total streams created
2. Total followers
3. Most followed streams (by follower engagement)
4. Average notification click-through rate (future phase)
5. Growth chart (followers over time)

Display as:
- Cards with numbers and icons
- Simple charts (use recharts library if available)
- Time range filter (7 days, 30 days, all time)

Requirements:
- Query Supabase efficiently
- Cache results
- Mobile-responsive
- Loading states

Provide complete component code.
```

### Phase 3 Completion Checklist

- [ ] Creator dashboard displays correctly
- [ ] Create new stream form works end-to-end
- [ ] YouTube URL verification works
- [ ] Twitch URL verification works
- [ ] Multi-platform streams save correctly
- [ ] Edit stream form pre-fills data
- [ ] Delete stream works with confirmation
- [ ] Draft/publish workflow functions
- [ ] Public creator page displays
- [ ] Public page is SEO-friendly (meta tags, SSR)
- [ ] All forms validate properly
- [ ] Error messages are clear and helpful

### Testing Checklist

**Test creator workflows:**
1. Create stream with YouTube only
2. Create stream with Twitch only
3. Create multi-platform stream (YouTube + Twitch)
4. Save stream as draft
5. Publish draft stream
6. Edit published stream
7. Delete stream
8. Verify invalid URLs are rejected
9. Check public page displays correctly
10. Test on mobile device

**AI Prompt for Testing:**
```
Create a comprehensive test plan for creator features including:
1. Form validation scenarios
2. URL verification edge cases
3. Multi-platform handling
4. Draft/publish workflow
5. Database integrity checks
6. Public page rendering
7. Mobile responsiveness

Provide detailed test cases with expected results.
```

### Handoff to Phase 4

**Before moving to Phase 4:**
- [ ] All creator features tested and working
- [ ] Public pages accessible and SEO-optimized
- [ ] Stream verification API functioning
- [ ] Database has sample stream data
- [ ] Code reviewed and merged
- [ ] Documentation updated with new routes

---

## Phase 4: Consumer Features

**Duration:** 2-3 weeks (25-35 hours)  
**Goal:** Fans can discover, follow creators, and view aggregated schedules  
**Dependencies:** Phase 3 complete

### Deliverables
- [ ] Creator search functionality
- [ ] Follow/unfollow system
- [ ] Aggregated schedule feed
- [ ] Filtering and sorting
- [ ] Consumer dashboard
- [ ] Timezone-aware display

### Tasks

#### Task 4.1: Create Creator Search (4 hours)

**What you're doing:** Letting users find VTubers by name or platform.

**Instructions:**

**AI Prompt:**
```
Create a creator search system.

Files needed:
1. app/(dashboard)/search/page.tsx - search UI
2. app/api/search/creators/route.ts - search API
3. components/search/creator-card.tsx - result display

Search should:
- Accept text input (creator name or platform username)
- Search in parallel:
  - Local database (existing creators)
  - YouTube API (by channel name)
  - Twitch API (by username)
- Display results with:
  - Avatar
  - Creator name
  - Platform badges
  - Follower count
  - "Follow" button
  - Link to public page
- Handle "Creator not in our database yet" case
  - Show "Add to platform" button
  - Creates creator record when clicked

Requirements:
- Debounced search (300ms delay)
- Loading skeleton during search
- Empty state ("No results found")
- Mobile-responsive grid
- TypeScript types

Provide all three files with complete code.

I have these API credentials:
- YOUTUBE_API_KEY
- TWITCH_CLIENT_ID
- TWITCH_CLIENT_SECRET
```

#### Task 4.2: Implement Follow/Unfollow System (3 hours)

**What you're doing:** Letting users follow their favorite creators.

**Instructions:**

**AI Prompt:**
```
Create follow/unfollow functionality.

Files needed:
1. app/actions/follow.ts - Server Actions
2. components/creator/follow-button.tsx - UI component
3. lib/db/follows.ts - Database functions

Functions:
1. followCreator(userId, creatorId)
   - Inserts into 'follows' table
   - Updates creator follower_count
   - Returns success or error

2. unfollowCreator(userId, creatorId)
   - Deletes from 'follows' table
   - Updates creator follower_count
   - Returns success or error

3. isFollowing(userId, creatorId)
   - Checks if follow relationship exists
   - Returns boolean

4. getFollowedCreators(userId)
   - Returns list of creators user follows
   - Includes creator details

Follow button should:
- Show "Follow" or "Following" based on state
- Toggle on click
- Show loading state
- Display follower count
- Work for logged-out users (prompt to log in)

Requirements:
- Optimistic UI updates
- Handle duplicate follow attempts
- Proper error handling
- TypeScript types

Provide all code files.
```

#### Task 4.3: Build Consumer Dashboard (5 hours)

**What you're doing:** Main page consumers see showing upcoming streams.

**Instructions:**

**AI Prompt:**
```
Create consumer dashboard showing aggregated schedule feed.

File: app/(dashboard)/feed/page.tsx

Dashboard should display:
1. Header:
   - "Your Schedule" title
   - Filter controls
   - View toggle (List/Calendar)

2. Filters:
   - Platform (YouTube, Twitch, All)
   - Creator (dropdown of followed creators)
   - Date range (Today, This Week, This Month, Custom)
   - Time of day (Morning, Afternoon, Evening, Night, All)

3. Stream feed:
   - Chronological list of upcoming streams
   - Each stream card shows:
     - Creator avatar + name
     - Stream title
     - Platform badge(s)
     - Date/time in user's timezone
     - Countdown if within 24 hours
     - "Remind Me" toggle
     - Link to stream
   - Group by date ("Today", "Tomorrow", "May 15")
   - Infinite scroll or pagination

4. Empty state if not following anyone:
   - Friendly message
   - "Discover Creators" CTA button

Requirements:
- Fetch from Supabase with joins
- Client-side filtering for responsiveness
- Loading skeletons
- Mobile-responsive
- Update in real-time when creator publishes new stream
- Show "NEW" badge for recently published streams

Provide complete component code.
```

#### Task 4.4: Implement Timezone-Aware Display (3 hours)

**What you're doing:** Showing times in the user's local timezone.

**Instructions:**

**AI Prompt:**
```
Create timezone-aware date/time display system.

Files needed:
1. lib/utils/timezone.ts - timezone utilities (enhance existing)
2. components/ui/datetime-display.tsx - reusable component
3. hooks/useUserTimezone.ts - React hook

Enhancements:
1. formatStreamTime(isoDate, userTimezone)
   - Formats: "Today at 7:00 PM PST"
   - "Tomorrow at 9:00 AM EST"
   - "May 15 at 3:00 PM JST"
   - "In 2 hours" (if within 6 hours)

2. getCountdown(streamStart)
   - Returns: "Starts in 2h 30m"
   - Updates every minute
   - Shows "LIVE NOW" if currently streaming

3. DateTimeDisplay component:
   - Props: isoDate, format, showCountdown, showTimezone
   - Displays formatted time
   - Optional countdown
   - Tooltip with absolute time

Requirements:
- Use user's timezone from profile
- Handle DST transitions
- Update countdown in real-time
- Mobile-friendly formatting
- Accessible (ARIA labels)

Provide all three files.

I'm using date-fns for date manipulation.
```

#### Task 4.5: Add Filtering and Sorting (3 hours)

**What you're doing:** Letting users filter their schedule feed.

**Instructions:**

**AI Prompt:**
```
Create advanced filtering and sorting for the schedule feed.

File: components/feed/feed-filters.tsx

Filters:
1. Platform (multi-select: YouTube, Twitch)
2. Creators (multi-select dropdown with search)
3. Date range:
   - Quick picks: Today, Tomorrow, This Week, This Month
   - Custom range picker
4. Time of day (Morning, Afternoon, Evening, Night)
5. Tags (if streams have tags)

Sorting:
1. Chronological (default)
2. By creator name
3. By follower count

Filter component should:
- Apply filters client-side for speed
- Update URL params (shareable filtered view)
- Show active filter count
- "Clear all" button
- Collapse on mobile (drawer/sheet)
- Persist preferences (localStorage)

Provide:
- Filter component
- Filter logic functions
- URL param handling

Complete code with TypeScript.
```

#### Task 4.6: Create Calendar View (Optional, 4 hours)

**What you're doing:** Alternative view showing streams in a calendar.

**Instructions:**

**AI Prompt:**
```
Create a calendar view for the schedule feed.

File: components/feed/calendar-view.tsx

Calendar should:
- Display month view by default
- Show streams as events on dates
- Click date to see streams that day
- Color-code by platform
- Show creator avatar in event
- Tooltip on hover with stream details
- Navigate prev/next month
- "Today" button to jump to current date
- Mobile: Switch to list view for small screens

Requirements:
- Use a calendar library if available, or build custom
- Handle timezone conversions
- Show multi-day events (long streams)
- Responsive design

If using a library, suggest one and provide integration code.
If building custom, provide complete calendar component.

Provide complete implementation.
```

#### Task 4.7: Add Quick Actions (2 hours)

**What you're doing:** Convenient actions directly from the feed.

**Instructions:**

**AI Prompt:**
```
Add quick action buttons to stream cards in the feed.

Actions to add:
1. "Remind Me" toggle
   - Sets notification for this specific stream
   - Overrides user's default notification settings
   - Visual indicator when enabled
   - Syncs to 'notifications' table

2. "Share" button
   - Copy link to stream
   - Share to Twitter (pre-filled tweet)
   - Share to Discord (if possible)
   - Shows success message

3. "Add to Calendar" (optional)
   - Downloads .ics file
   - Works with Google Calendar, Outlook, Apple Calendar

4. "More" menu:
   - Report issue with schedule
   - Suggest edit
   - Hide this stream

Update:
- components/feed/stream-card.tsx
- Add action handlers
- Create share dialog component

Provide all updated code.
```

#### Task 4.8: Implement Real-Time Updates (3 hours)

**What you're doing:** Auto-updating feed when creators publish new streams.

**Instructions:**

**AI Prompt:**
```
Add real-time updates to the schedule feed using Supabase Realtime.

Requirements:
1. Subscribe to 'streams' table changes
2. When new stream published:
   - Add to feed if user follows creator
   - Show "NEW" badge
   - Optionally show toast notification

3. When stream updated:
   - Update card in place
   - Highlight changed fields briefly

4. When stream deleted:
   - Remove from feed smoothly

Implementation:
- Use Supabase Realtime subscriptions
- Filter events client-side (only followed creators)
- Optimize to prevent excessive re-renders
- Unsubscribe on component unmount

Update:
- app/(dashboard)/feed/page.tsx
- Add realtime subscription logic

Provide complete implementation.

I have @supabase/supabase-js installed with Realtime support.
```

### Phase 4 Completion Checklist

- [ ] Creator search works (local + external APIs)
- [ ] Follow/unfollow buttons function correctly
- [ ] Consumer dashboard displays aggregated feed
- [ ] All filters work as expected
- [ ] Sorting options function
- [ ] Timezone displays correctly for all users
- [ ] Countdown timers update in real-time
- [ ] Calendar view displays (if implemented)
- [ ] Quick actions work (remind, share)
- [ ] Real-time updates appear without refresh
- [ ] Empty states display appropriately
- [ ] Mobile-responsive on all screens

### Testing Checklist

**Test consumer workflows:**
1. Search for creator by name
2. Search for creator by YouTube channel
3. Search for creator by Twitch username
4. Follow a creator
5. Unfollow a creator
6. View feed with followed creators
7. Apply each filter type
8. Switch between list and calendar view
9. Check times display in correct timezone
10. Test real-time update when creator publishes

**Edge cases to test:**
- Following 0 creators (empty state)
- Following 50+ creators (performance)
- Streams in different timezones
- Past streams don't appear
- Cancelled streams marked clearly

**AI Prompt for Testing:**
```
Create a test plan for consumer features including:
1. Search functionality (various input types)
2. Follow system edge cases
3. Feed filtering and performance
4. Timezone handling across DST
5. Real-time update scenarios
6. Mobile user experience

Provide detailed test cases.
```

### Handoff to Phase 5

**Before moving to Phase 5:**
- [ ] All consumer features tested
- [ ] Feed performance optimized (loads < 2 seconds)
- [ ] Real-time subscriptions working
- [ ] No memory leaks from subscriptions
- [ ] Code reviewed and merged
- [ ] User feedback collected on UX

---

## Phase 5: Notifications System

**Duration:** 1-2 weeks (15-20 hours)  
**Goal:** Users receive email and push notifications for upcoming streams  
**Dependencies:** Phase 4 complete

### Deliverables
- [ ] Email notifications via Resend
- [ ] Browser push notifications
- [ ] Notification preferences management
- [ ] Scheduled notification jobs
- [ ] Notification history

### Tasks

#### Task 5.1: Set Up Resend Email Templates (3 hours)

**What you're doing:** Creating professional email templates for notifications.

**Instructions:**

**AI Prompt:**
```
Create email templates for stream notifications using Resend.

Files needed:
1. lib/emails/stream-reminder.tsx
2. lib/emails/schedule-change.tsx
3. lib/emails/new-stream.tsx

Each template should:
- Use React Email components (@react-email/components)
- Be mobile-responsive
- Include:
  - App logo/branding
  - Creator avatar
  - Stream title
  - Date/time in recipient's timezone
  - Platform badges
  - "Watch Now" button (links to stream)
  - Unsubscribe link
  - Social links footer

Templates needed:

1. Stream Reminder Email:
   - Subject: "[Creator] is going live in [time]!"
   - Body: Countdown, stream details, CTA

2. Schedule Change Email:
   - Subject: "Schedule Update: [Stream Title]"
   - Body: What changed (time/platform/title), new details

3. New Stream Announced Email:
   - Subject: "[Creator] just scheduled a new stream!"
   - Body: Stream details, add to calendar

Requirements:
- Professional design
- Inline CSS (email client compatibility)
- Dark mode support if possible
- Accessibility (alt text, semantic HTML)

Provide all three email template files.

I have @react-email/components installed.
```

#### Task 5.2: Implement Email Sending Functions (2 hours)

**What you're doing:** Functions to actually send the emails.

**Instructions:**

**AI Prompt:**
```
Create email sending functions using Resend.

File: lib/email/send.ts

Functions needed:
1. sendStreamReminder(user, stream, reminderTime)
   - Sends reminder email
   - Uses stream-reminder template
   - Tracks in 'notifications' table

2. sendScheduleChange(user, stream, changes)
   - Sends schedule update email
   - Uses schedule-change template
   - Includes what changed

3. sendNewStreamNotification(user, stream)
   - Sends new stream announcement
   - Uses new-stream template

Helper functions:
- queueEmail(notificationData) - adds to send queue
- batchSendEmails(emails[]) - sends multiple at once
- logEmailResult(notificationId, result) - tracks delivery

Requirements:
- Use Resend API
- Rate limiting (max 100 emails/minute)
- Retry logic for failures
- Proper error handling
- Track sends in 'notifications' table
- Unsubscribe handling

Provide complete file with all functions.

My Resend API key is in: process.env.RESEND_API_KEY
```

#### Task 5.3: Set Up Browser Push Notifications (4 hours)

**What you're doing:** Implementing web push notifications.

**Instructions:**

**AI Prompt:**
```
Implement browser push notifications using Web Push API.

Files needed:
1. lib/notifications/push.ts - push functions
2. public/sw.js - service worker
3. components/notifications/push-permission.tsx - permission UI

Setup process:
1. Generate VAPID keys (provide command)
2. Register service worker
3. Request notification permission
4. Subscribe user to push
5. Store subscription in database
6. Send push notifications

Push notification should include:
- Creator avatar (icon)
- Stream title
- "Starting in X minutes"
- Click action: open stream URL

Components:
1. PushPermission component:
   - Button to enable push
   - Explains what they'll receive
   - Handles permission request
   - Stores subscription

2. Service worker (sw.js):
   - Handles push events
   - Shows notification
   - Handles click action

3. Push sending function:
   - Takes notification data
   - Sends to all subscribed users
   - Handles errors/expired subscriptions

Requirements:
- Works on Chrome, Firefox, Edge, Safari 16+
- Handles denied permission gracefully
- Unsubscribe option
- Proper error handling

Provide all files needed for complete implementation.

Note: I need help generating VAPID keys too.
```

#### Task 5.4: Create Notification Preferences UI (2 hours)

**What you're doing:** Comprehensive settings for notification control.

**Instructions:**

**AI Prompt:**
```
Create an advanced notification preferences page.

File: app/(dashboard)/settings/notifications/page.tsx

Settings sections:

1. Master Controls:
   - Enable/disable all notifications
   - Email notifications toggle
   - Push notifications toggle

2. Timing Preferences:
   - When to send reminders:
     □ 30 minutes before
     □ 1 hour before
     □ 1 day before
     □ At stream start
   - Allow multiple selections

3. Content Preferences:
   - Notify for new streams
   - Notify for schedule changes
   - Notify for stream cancellations

4. Per-Creator Controls:
   - List of followed creators
   - Toggle notifications for each
   - "Mute all" / "Unmute all" buttons

5. Quiet Hours (Optional):
   - Don't send between X and Y time
   - User's local timezone

6. Notification History:
   - Last 50 notifications sent
   - Status (sent/failed)
   - Click-through tracking

Requirements:
- Save to 'profiles' table
- Real-time save (no submit button)
- Show save status indicator
- Test notification button
- Mobile-responsive
- Clear explanations for each setting

Provide complete component code.
```

#### Task 5.5: Implement Notification Scheduling System (4 hours)

**What you're doing:** Cron jobs that check for upcoming streams and send notifications.

**Instructions:**

**AI Prompt:**
```
Create a notification scheduling system using Vercel Cron Jobs.

Files needed:
1. app/api/cron/send-notifications/route.ts - main cron endpoint
2. lib/notifications/scheduler.ts - scheduling logic

Cron job should:
- Run every 15 minutes
- Query streams starting soon (based on user preferences)
- Get users who follow those creators
- Filter by user notification settings
- Check if notification already sent (dedupe)
- Send emails and/or push notifications
- Log results in 'notifications' table
- Handle rate limits

Logic flow:
1. Get streams in next: [30min, 1hour, 1day] windows
2. For each stream:
   - Get followers
   - Filter by notification preferences
   - Check timing preference (30min? 1hr? etc.)
   - Check if already notified
   - Queue notification
3. Batch send all queued notifications
4. Update database with results

Security:
- Verify cron secret header
- Prevent unauthorized access

Error handling:
- Retry failed notifications (max 3 times)
- Log all errors
- Continue on individual failures

Provide:
- Cron route handler
- Scheduler functions
- Database queries

Also provide vercel.json cron configuration.

My CRON_SECRET is in environment variables.
```

#### Task 5.6: Add Notification History & Management (2 hours)

**What you're doing:** Letting users see past notifications and manage settings.

**Instructions:**

**AI Prompt:**
```
Create notification history and management interface.

File: app/(dashboard)/settings/notification-history/page.tsx

Display:
1. List of recent notifications (last 100)
   - Each showing:
     - Type (reminder, new stream, schedule change)
     - Creator + stream name
     - Sent date/time
     - Status (sent, failed, clicked)
     - Delivery method (email, push, both)

2. Filters:
   - By type
   - By creator
   - By status
   - Date range

3. Actions:
   - Clear history
   - Export as CSV
   - Resend failed notifications

4. Statistics:
   - Total sent this month
   - Delivery rate
   - Click-through rate

Requirements:
- Paginated (20 per page)
- Fetch from 'notifications' table
- Mobile-responsive table/cards
- Loading states

Provide complete component code.
```

### Phase 5 Completion Checklist

- [ ] Email templates render correctly in email clients
- [ ] Stream reminder emails send successfully
- [ ] Schedule change emails send successfully
- [ ] Browser push notifications work (when permission granted)
- [ ] Push notifications open correct URL when clicked
- [ ] Notification preferences save correctly
- [ ] Per-creator notification muting works
- [ ] Cron job runs every 15 minutes
- [ ] Notifications are deduplicated (no spam)
- [ ] Notification history displays
- [ ] Unsubscribe links work
- [ ] Rate limiting prevents API abuse

### Testing Checklist

**Test notification flows:**
1. Enable email notifications
2. Follow a creator
3. Creator schedules stream in 1 hour
4. Wait for cron job to run
5. Verify email received
6. Enable push notifications
7. Grant browser permission
8. Verify push notification received ] Next.js project initialized
- [ ] Git repository created and configured
- [ ] Development environment working locally
- [ ] Basic project structure established
- [ ] Team has access to all accounts

### Tasks

#### Task 0.1: Initialize Next.js Project (2 hours)

**What you're doing:** Creating the foundation of your app.

**Instructions:**

1. **Open your terminal/command prompt**

2. **Navigate to where you want the project:**
   ```bash
   cd Documents/Projects
   ```

3. **Create the Next.js app:**
   ```bash
   npx create-next-app@latest vtuber-scheduler
   ```

4. **Answer the setup questions:**
   - TypeScript? → **Yes**
   - ESLint? → **Yes**
   - Tailwind CSS? → **Yes**
   - `/src` directory? → **No**
   - App Router? → **Yes**
   - Import alias? → **Yes** (use `@/*`)

5. **Navigate into the project:**
   ```bash
   cd vtuber-scheduler
   ```

6. **Open in VS Code:**
   ```bash
   code .
   ```

**AI Prompt to Use:**
```
I'm starting a new Next.js 14 project for a VTuber scheduling platform. 
I've just run create-next-app. Can you help me:
1. Explain the folder structure I'm seeing
2. Show me what each folder is for
3. Tell me which files I should focus on first
```

#### Task 0.2: Set Up Git Repository (1 hour)

**What you're doing:** Version control so you can track changes and collaborate.

**Instructions:**

1. **Initialize Git (if not already done):**
   ```bash
   git init
   ```

2. **Create a `.gitignore` file** (should already exist, verify it includes):
   ```
   node_modules/
   .next/
   .env*.local
   .vercel
   ```

3. **Make first commit:**
   ```bash
   git add .
   git commit -m "Initial Next.js setup"
   ```

4. **Create GitHub repository:**
   - Go to [github.com](https://github.com)
   - Click "New repository"
   - Name: `vtuber-scheduler`
   - Privacy: Private (recommended for now)
   - Don't initialize with README (you already have one)
   - Click "Create repository"

5. **Link local project to GitHub:**
   ```bash
   git remote add origin https://github.com/YOUR-USERNAME/vtuber-scheduler.git
   git branch -M main
   git push -u origin main
   ```

**AI Prompt to Use:**
```
I need to set up Git for my project. I want to:
1. Create a proper .gitignore file for Next.js
2. Make sure my environment variables stay private
3. Set up a good commit message structure
Can you help me with these?
```

#### Task 0.3: Install Required Dependencies (1 hour)

**What you're doing:** Adding the packages your app needs.

**Instructions:**

1. **Install Supabase client:**
   ```bash
   npm install @supabase/supabase-js @supabase/ssr
   ```

2. **Install additional UI libraries:**
   ```bash
   npm install @radix-ui/react-dialog @radix-ui/react-dropdown-menu
   npm install @radix-ui/react-select @radix-ui/react-toast
   npm install class-variance-authority clsx tailwind-merge
   npm install lucide-react
   npm install date-fns
   ```

3. **Install development tools:**
   ```bash
   npm install -D @types/node
   ```

4. **Verify installation:**
   ```bash
   npm run dev
   ```
   Visit `http://localhost:3000` - you should see the Next.js welcome page

**AI Prompt to Use:**
```
I've installed these packages for my Next.js app: [list packages].
Can you explain:
1. What each package does
2. How I'll use them in my VTuber scheduling app
3. Are there any other packages I might need for this project?
```

#### Task 0.4: Set Up Environment Variables (1 hour)

**What you're doing:** Storing your API keys securely.

**Instructions:**

1. **Create `.env.local` file in project root:**
   ```bash
   # In VS Code, right-click root folder → New File → .env.local
   ```

2. **Add your credentials** (from your secure notes):
   ```bash
   # Supabase
   NEXT_PUBLIC_SUPABASE_URL=your-supabase-url
   NEXT_PUBLIC_SUPABASE_ANON_KEY=your-anon-key
   SUPABASE_SERVICE_ROLE_KEY=your-service-role-key
   
   # YouTube API
   YOUTUBE_API_KEY=your-youtube-key
   
   # Twitch API
   TWITCH_CLIENT_ID=your-twitch-client-id
   TWITCH_CLIENT_SECRET=your-twitch-secret
   
   # Resend
   RESEND_API_KEY=your-resend-key
   
   # App
   NEXT_PUBLIC_APP_URL=http://localhost:3000
   ```

3. **Create `.env.example` for team members:**
   ```bash
   # Copy .env.local to .env.example
   # Replace all values with placeholders
   NEXT_PUBLIC_SUPABASE_URL=your-supabase-url-here
   # ... etc
   ```

4. **Verify variables load:**
   - Create `app/test/page.tsx`:
   ```typescript
   export default function TestEnv() {
     return (
       <div>
         <h1>Environment Test</h1>
         <p>Supabase URL: {process.env.NEXT_PUBLIC_SUPABASE_URL ? '✅ Set' : '❌ Missing'}</p>
       </div>
     )
   }
   ```
   - Visit `http://localhost:3000/test`

**AI Prompt to Use:**
```
I need to set up environment variables for my Next.js app. I have:
- Supabase credentials
- YouTube API key
- Twitch API credentials
- Resend API key

Can you show me:
1. The proper format for .env.local
2. Which variables should be NEXT_PUBLIC_ and which shouldn't
3. How to access these in my code
```

#### Task 0.5: Create Project Structure (2 hours)

**What you're doing:** Organizing your code into logical folders.

**Instructions:**

1. **Create folder structure:**
   ```bash
   # In terminal or VS Code, create these folders:
   mkdir -p app/(auth)
   mkdir -p app/(dashboard)
   mkdir -p app/creator
   mkdir -p components/ui
   mkdir -p components/auth
   mkdir -p components/creator
   mkdir -p components/consumer
   mkdir -p lib/supabase
   mkdir -p lib/api
   mkdir -p types
   ```

2. **Verify structure looks like this:**
   ```
   vtuber-scheduler/
   ├── app/
   │   ├── (auth)/          ← Login/signup pages
   │   ├── (dashboard)/     ← User dashboard pages
   │   ├── creator/         ← Public creator pages
   │   ├── api/            ← API routes (auto-created)
   │   ├── layout.tsx
   │   └── page.tsx
   ├── components/
   │   ├── ui/             ← Reusable UI components
   │   ├── auth/           ← Auth-related components
   │   ├── creator/        ← Creator-specific components
   │   └── consumer/       ← Consumer-specific components
   ├── lib/
   │   ├── supabase/       ← Supabase utilities
   │   └── api/            ← External API helpers
   ├── types/              ← TypeScript type definitions
   └── public/             ← Static assets
   ```

**AI Prompt to Use:**
```
I'm setting up the folder structure for a Next.js app with:
- User authentication
- Creator dashboards
- Consumer dashboards
- Public creator pages
- API routes

Can you suggest:
1. An optimal folder structure
2. What files should go in each folder
3. Naming conventions to follow
```

#### Task 0.6: Set Up Development Workflow (2 hours)

**What you're doing:** Establishing how the team will work together.

**Instructions:**

1. **Create a README.md:**
   ```markdown
   # VTuber Scheduler
   
   ## Getting Started
   
   1. Clone the repository
   2. Copy `.env.example` to `.env.local`
   3. Fill in your credentials
   4. Run `npm install`
   5. Run `npm run dev`
   6. Visit `http://localhost:3000`
   
   ## Project Structure
   [Document your folder structure]
   
   ## Development Workflow
   1. Create a new branch for each feature
   2. Make changes
   3. Test locally
   4. Push and create Pull Request
   5. Review and merge
   ```

2. **Set up Git branches:**
   ```bash
   git checkout -b develop
   git push -u origin develop
   ```

3. **Create a project board:**
   - Go to GitHub repository
   - Click "Projects" tab
   - Create new project (Board view)
   - Add columns: Backlog, In Progress, Review, Done

**AI Prompt to Use:**
```
Help me create a README.md for my VTuber scheduling project that includes:
1. Setup instructions for new developers
2. Project structure explanation
3. How to run the app locally
4. Contribution guidelines
```

#### Task 0.7: Connect to Vercel (2 hours)

**What you're doing:** Setting up automatic deployments.

**Instructions:**

1. **Log in to Vercel:** [vercel.com](https://vercel.com)

2. **Import project:**
   - Click "Add New" → "Project"
   - Select your GitHub repository
   - Framework: Next.js (auto-detected)
   - Click "Deploy"

3. **Add environment variables:**
   - Project Settings → Environment Variables
   - Add all variables from `.env.local`
   - Select: Production, Preview, Development
   - Save

4. **Test deployment:**
   - Push a change to GitHub
   - Watch automatic deployment
   - Visit your Vercel URL

**AI Prompt to Use:**
```
I'm deploying my Next.js app to Vercel. Can you help me:
1. Understand what happens during deployment
2. Set up automatic deployments from GitHub
3. Configure environment variables correctly
4. Set up preview deployments for pull requests
```

### Phase 0 Completion Checklist

- [ ] Next.js app running locally
- [ ] Git repository created and pushed to GitHub
- [ ] All dependencies installed
- [ ] Environment variables configured
- [ ] Folder structure created
- [ ] README documentation written
- [ ] Vercel connected and first deployment successful
- [ ] Team members have access to all accounts
- [ ] Development workflow established

### Handoff to Phase 1

**Before moving to Phase 1:**
1. Ensure everyone can run the app locally
2. Test that Vercel deployments work
3. Verify all team members have necessary access
4. Review and approve folder structure

---

## Phase 1: User Authentication

**Duration:** 1-2 weeks (15-20 hours)  
**Goal:** Users can sign up and log in with YouTube, Twitch, and Twitter  
**Dependencies:** Phase 0 complete

### Deliverables
- [ ] Login page with OAuth buttons
- [ ] Sign-up flow working
- [ ] User session management
- [ ] Protected routes (dashboard)
- [ ] Basic user profile creation

### Tasks

#### Task 1.1: Create Supabase Client Utilities (2 hours)

**What you're doing:** Setting up helper functions to talk to Supabase.

**Instructions:**

1. **Create `lib/supabase/client.ts`:**

**AI Prompt:**
```
I need to create Supabase client utilities for my Next.js 14 app using App Router. 
I need:
1. A client for Client Components
2. A client for Server Components
3. A client for Server Actions
4. Proper TypeScript types

My environment variables are:
- NEXT_PUBLIC_SUPABASE_URL
- NEXT_PUBLIC_SUPABASE_ANON_KEY
- SUPABASE_SERVICE_ROLE_KEY

Can you provide the complete code for lib/supabase/client.ts?
```

2. **Create `lib/supabase/middleware.ts`:**

**AI Prompt:**
```
I need Next.js middleware to handle Supabase authentication. It should:
1. Refresh the user's session on each request
2. Protect routes that require authentication
3. Redirect unauthenticated users to login
4. Allow public routes

Can you provide the complete middleware.ts code?
```

3. **Test the clients:**

**AI Prompt:**
```
Help me create a test page to verify my Supabase clients work correctly.
Create app/test-auth/page.tsx that:
1. Tests the client connection
2. Shows if a user is logged in
3. Displays user info if authenticated
```

#### Task 1.2: Build Login Page UI (3 hours)

**What you're doing:** Creating the page where users sign in.

**Instructions:**

1. **Create `app/(auth)/login/page.tsx`:**

**AI Prompt:**
```
Create a modern, mobile-friendly login page for my VTuber scheduling app.
It should have:
1. App logo/title at the top
2. Three OAuth buttons: "Continue with Google/YouTube", "Continue with Twitch", "Continue with Twitter"
3. Each button should have the appropriate logo and brand colors
4. Tailwind CSS styling
5. Responsive design
6. Loading states

Use these packages I have installed:
- lucide-react for icons
- Tailwind CSS for styling

Provide the complete component code.
```

2. **Create OAuth button component** (`components/auth/oauth-button.tsx`):

**AI Prompt:**
```
Create a reusable OAuthButton component that:
1. Accepts props: provider (google/twitch/twitter), label, icon
2. Shows a loading spinner when clicked
3. Handles the OAuth redirect
4. Has proper TypeScript types
5. Uses Tailwind for styling with brand colors

Provide the complete component code.
```

3. **Style the auth pages:**

**AI Prompt:**
```
Create a layout for auth pages (app/(auth)/layout.tsx) that:
1. Centers content vertically and horizontally
2. Has a clean, modern design
3. Shows the app logo
4. Is mobile-responsive
5. Has a subtle gradient background

Provide the complete layout code.
```

#### Task 1.3: Implement OAuth Flows (4 hours)

**What you're doing:** Making the sign-in buttons actually work.

**Instructions:**

1. **Create auth helper functions** (`lib/auth/oauth.ts`):

**AI Prompt:**
```
Create authentication helper functions for my Supabase OAuth flows:

1. signInWithGoogle() - initiates Google OAuth
2. signInWithTwitch() - initiates Twitch OAuth  
3. signInWithTwitter() - initiates Twitter OAuth
4. handleOAuthCallback() - processes the callback after OAuth
5. signOut() - logs the user out

Requirements:
- Use Next.js App Router
- Use Supabase Auth
- Include error handling
- Include proper TypeScript types
- Redirect to /dashboard after successful login
- Redirect to /login on error

Provide complete code with comments.
```

2. **Create OAuth callback route** (`app/auth/callback/route.ts`):

**AI Prompt:**
```
Create an API route for handling OAuth callbacks from Supabase.
It should:
1. Exchange the code for a session
2. Set the session cookie
3. Redirect to the dashboard on success
4. Redirect to login with error message on failure
5. Handle all three providers (Google, Twitch, Twitter)

This is a Next.js 14 App Router route handler.
Provide the complete code.
```

3. **Wire up buttons to OAuth functions:**

**AI Prompt:**
```
Update my login page to call the OAuth functions when buttons are clicked.
Here's my current login page code: [paste your code]

Add:
1. onClick handlers for each button
2. Loading states while OAuth is processing
3. Error message display if OAuth fails
4. Proper async/await handling

Provide the updated component code.
```

#### Task 1.4: Create User Profile on First Login (3 hours)

**What you're doing:** Saving user info when they first sign up.

**Instructions:**

1. **Create profile creation function** (`lib/auth/profile.ts`):

**AI Prompt:**
```
Create a function that creates a user profile in Supabase after OAuth login.

Function: createUserProfile(userId: string, authProvider: 'google' | 'twitch' | 'twitter')

It should:
1. Check if profile already exists
2. If not, insert into 'profiles' table with:
   - id (from auth user)
   - username (generate from auth metadata)
   - display_name (from auth metadata)
   - avatar_url (from auth metadata)
   - youtube_channel_id (if Google)
   - twitch_username (if Twitch)
   - twitter_handle (if Twitter)
   - timezone (detect from browser)
3. Return the profile or error
4. Handle all edge cases

Provide complete TypeScript code with error handling.
```

2. **Update callback route to create profile:**

**AI Prompt:**
```
Update my OAuth callback route to create a user profile after successful authentication.

Current code: [paste callback route code]

Add:
1. Call createUserProfile() after session is established
2. Handle the case where profile already exists
3. Log any errors
4. Still redirect to dashboard even if profile creation fails

Provide the updated route handler code.
```

#### Task 1.5: Implement Protected Routes (2 hours)

**What you're doing:** Preventing non-logged-in users from accessing certain pages.

**Instructions:**

1. **Create auth middleware:**

**AI Prompt:**
```
Create Next.js middleware that protects routes requiring authentication.

Protected routes:
- /dashboard/*
- /settings/*
- /creator/manage/*

Public routes:
- /
- /login
- /creator/[username] (view only)

It should:
1. Check if user has valid session
2. Redirect to /login if not authenticated
3. Allow public routes without authentication
4. Use Supabase auth

File: middleware.ts at project root
Provide complete code.
```

2. **Test protected routes:**

**AI Prompt:**
```
Help me create a test dashboard page to verify route protection works.

Create app/(dashboard)/dashboard/page.tsx that:
1. Shows user's email and name
2. Has a "Sign Out" button
3. Is only accessible when logged in
4. Displays user's auth provider

Provide the complete component code.
```

#### Task 1.6: Add Sign Out Functionality (1 hour)

**What you're doing:** Letting users log out.

**Instructions:**

**AI Prompt:**
```
Create a sign-out function and UI component.

1. Create lib/auth/signout.ts with a signOut() function that:
   - Calls Supabase signOut()
   - Clears any local storage
   - Redirects to /login

2. Create components/auth/sign-out-button.tsx that:
   - Shows a "Sign Out" button
   - Calls the signOut function
   - Has a loading state
   - Confirms before signing out

Provide both complete code files.
```

#### Task 1.7: Session Persistence & State Management (2 hours)

**What you're doing:** Keeping users logged in across page refreshes.

**Instructions:**

**AI Prompt:**
```
Help me implement session persistence in my Next.js app with Supabase.

I need:
1. A React Context for auth state (components/auth/auth-provider.tsx)
2. useAuth hook to access current user anywhere
3. Auto-refresh session before expiry
4. Update UI when auth state changes

Provide:
- AuthProvider component
- useAuth hook
- How to wrap my app with the provider
- TypeScript types for the context

Complete code with comments.
```

### Phase 1 Completion Checklist

- [ ] Login page displays correctly on desktop and mobile
- [ ] Google/YouTube OAuth button works
- [ ] Twitch OAuth button works
- [ ] Twitter OAuth button works
- [ ] User profile created in database after first login
- [ ] User can sign out
- [ ] Protected routes redirect to login when not authenticated
- [ ] Session persists across page refreshes
- [ ] Error messages display for failed authentication
- [ ] Unit tests pass for auth functions

### Testing Checklist

**Manual Testing:**
1. Sign in with each provider (Google, Twitch, Twitter)
2. Verify profile created in Supabase Table Editor
3. Refresh page - should stay logged in
4. Sign out - should redirect to login
5. Try accessing /dashboard without login - should redirect
6. Test on mobile device

**AI Prompt for Testing Help:**
```
I've completed the authentication phase of my app. 
Help me create a testing checklist that covers:
1. All OAuth flows
2. Edge cases (network errors, cancelled OAuth, etc.)
3. Security considerations
4. Mobile testing
5. Session expiry scenarios

Provide a detailed testing plan.
```

### Handoff to Phase 2

**Before moving to Phase 2:**
- [ ] All authentication flows tested and working
- [ ] Profiles table has data from test logins
- [ ] Team can all log in successfully
- [ ] Code reviewed and merged to main branch
- [ ] Deployed to Vercel and tested in production

---

## Phase 2: Basic Database & User Profiles

**Duration:** 1-2 weeks (15-20 hours)  
**Goal:** Users can view and edit their profiles, set preferences  
**Dependencies:** Phase 1 complete

### Deliverables
- [ ] User profile page
- [ ] Profile editing functionality
- [ ] Timezone selection
- [ ] Notification preferences UI
- [ ] Avatar upload (optional)

### Tasks

#### Task 2.1: Create Profile Page (3 hours)

**What you're doing:** Building a page where users can see their info.

**Instructions:**

**AI Prompt:**
```
Create a user profile page for my VTuber scheduling app.

File: app/(dashboard)/profile/page.tsx

It should display:
1. User's avatar (from OAuth)
2. Display name
3. Username
4. Email
5. Connected accounts (YouTube, Twitch, Twitter) with badges
6. Account created date
7. Timezone setting
8. "Edit Profile" button

Requirements:
- Fetch data from Supabase 'profiles' table
- Use Server Component for initial data
- Mobile-responsive layout
- Tailwind CSS styling
- Loading state
- Error handling if profile not found

Provide complete code with TypeScript types.
```

#### Task 2.2: Build Profile Edit Form (4 hours)

**What you're doing:** Letting users change their profile information.

**Instructions:**

**AI Prompt:**
```
Create a profile edit form component.

File: components/profile/edit-profile-form.tsx

Form fields:
1. Display Name (text input)
2. Username (text input, must be unique)
3. Bio (textarea, optional, 500 char max)
4. Timezone (dropdown with common timezones)
5. Save and Cancel buttons

Requirements:
- Use React Hook Form for validation
- Check username availability as user types
- Show validation errors
- Disable save button if no changes
- Loading state on save
- Success/error toast messages
- Update Supabase 'profiles' table
- Client Component with Server Actions

Provide complete component code.

Note: I have these packages installed: react-hook-form, zod
```

#### Task 2.3: Implement Server Actions for Profile Updates (2 hours)

**What you're doing:** Creating backend functions to save profile changes.

**Instructions:**

**AI Prompt:**
```
Create Server Actions for profile management.

File: app/actions/profile.ts

Functions needed:
1. updateProfile(userId, data) - updates profile fields
2. checkUsernameAvailable(username) - returns boolean
3. getProfile(userId) - fetches profile data
4. updateTimezone(userId, timezone) - updates user timezone

Requirements:
- Use "use server" directive
- Validate inputs with Zod
- Use Supabase service role for updates
- Return proper error messages
- Include TypeScript types
- Handle edge cases (user not found, duplicate username, etc.)

Provide complete code with validation schemas.
```

#### Task 2.4: Add Timezone Detection & Selection (2 hours)

**What you're doing:** Automatically detecting user's timezone and letting them change it.

**Instructions:**

**AI Prompt:**
```
Create timezone functionality for my app.

1. Create lib/utils/timezone.ts with:
   - detectTimezone() - gets user's current timezone
   - formatInTimezone(date, timezone) - formats date in specific timezone
   - timezoneOptions - list of common timezones for dropdown

2. Create components/profile/timezone-selector.tsx:
   - Dropdown with searchable timezones
   - Shows current timezone
   - Displays time in selected timezone as preview
   - Auto-detects on first load

Requirements:
- Use date-fns-tz for timezone handling
- Group timezones by region
- Mobile-friendly dropdown
- Save preference to database

Provide both complete files.
```

#### Task 2.5: Create Notification Preferences UI (3 hours)

**What you're doing:** UI for users to control how they receive notifications.

**Instructions:**

**AI Prompt:**
```
Create a notification preferences component.

File: components/profile/notification-preferences.tsx

Settings to include:
1. Master toggle: Enable/Disable all notifications
2. Email notifications toggle
3. Push notifications toggle
4. Notification timing dropdown:
   - 30 minutes before
   - 1 hour before
   - 1 day before
   - At stream start time
5. Per-creator muting (for future phase, just UI for now)

Requirements:
- Clear labels and descriptions
- Save changes to 'profiles' table
- Show save status
- Disable child toggles when master is off
- Mobile-responsive
- Use shadcn/ui components if available

Provide complete component code.
```

#### Task 2.6: Implement "Creator Mode" Toggle (2 hours)

**What you're doing:** Letting users enable creator features.

**Instructions:**

**AI Prompt:**
```
Create functionality for users to become creators.

1. Add "Enable Creator Mode" toggle to profile page
2. When enabled:
   - Set 'is_creator' to true in profiles table
   - Create entry in 'creators' table with default settings
   - Redirect to creator onboarding (Phase 3)
3. When disabled:
   - Show confirmation dialog
   - Set 'is_creator' to false
   - Keep 'creators' table entry (don't delete, just inactive)

Create:
- Server action: toggleCreatorMode(userId, enabled)
- UI component: components/profile/creator-mode-toggle.tsx
- Confirmation dialog component

Provide all code files.
```

#### Task 2.7: Add Avatar Upload (Optional, 2 hours)

**What you're doing:** Letting users upload custom profile pictures.

**Instructions:**

**AI Prompt:**
```
Create avatar upload functionality using Supabase Storage.

Requirements:
1. Upload image to Supabase Storage bucket 'avatars'
2. Resize/crop to square (400x400px)
3. File size limit: 2MB
4. Accepted formats: JPG, PNG, WebP
5. Show upload progress
6. Preview before saving
7. Update 'avatar_url' in profiles table

Create:
- components/profile/avatar-upload.tsx - upload UI component
- lib/storage/avatar.ts - upload/delete functions
- Server action to update avatar URL in database

Provide complete code for all files.

Note: I have Supabase Storage enabled.
```

### Phase 2 Completion Checklist

- [ ] Profile page displays user information correctly
- [ ] Users can edit display name and username
- [ ] Username uniqueness validation works
- [ ] Timezone auto-detects and can be changed
- [ ] Timezone affects date/time display throughout app
- [ ] Notification preferences save correctly
- [ ] Creator mode toggle works
- [ ] Avatar upload works (if implemented)
- [ ] All changes persist after page refresh
- [ ] Mobile-responsive on all screen sizes

### Testing Checklist

**Test each feature:**
1. Load profile page - all data displays
2. Edit profile - changes save and appear immediately
3. Try duplicate username - shows error
4. Change timezone - dates update throughout app
5. Toggle notification settings - saves correctly
6. Enable creator mode - redirects appropriately
7. Upload avatar - appears immediately (if implemented)

**AI Prompt for Testing:**
```
Create a test plan for the profile management features including:
1. Edge cases (empty fields, long text, special characters)
2. Database validation
3. UI state management
4. Error scenarios
5. Mobile testing checklist

Provide detailed test cases.
```

### Handoff to Phase 3

**Before moving to Phase 3:**
- [ ] All profile features tested
- [ ] Database queries optimized
- [ ] Error handling complete
- [ ] Code reviewed and merged
- [ ] Documentation updated

---

## Phase 3: Creator Features

**Duration:** 2-3 weeks (30-40 hours)  
**Goal:** Creators can create, edit, and manage stream schedules  
**Dependencies:** Phase 2 complete

### Deliverables
- [