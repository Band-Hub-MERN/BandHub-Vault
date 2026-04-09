# Mobile App — Initial Setup Context

## Background

The BandHub / Garage Jam project is a MERN stack web application built for UCF students to claim parking garage practice spots and discover campus events. The web app is fully functional with a React/Vite/TypeScript frontend and an Express/MongoDB backend.

This document records the starting context before mobile development began.

---

## Existing Mobile Skeleton

Located at: `mobile_app/GarageBand AndroidStudio/`

Type: Native Android (Kotlin + XML layouts + View Binding + Navigation Component)

What the skeleton had:
- `MainActivity.kt` — bottom nav wired to NavController
- Three fragments: `HomeFragment`, `TicketsFragment`, `SettingsFragment`
- `HomeFragment` — calendar grid + followed bands list (all mock data)
- `TicketsFragment` — ViewPager2 with Upcoming / Past tabs
- `SettingsFragment` — static rows (Username, Email, Notifications, etc.) with Toast placeholders
- `ArtistDetailActivity`, `TicketDetailActivity` — detail screens
- Adapters: `ArtistAdapter`, `CalendarAdapter`, `TicketAdapter`, `VenueAdapter`, `TicketsPagerAdapter`
- Models: `Artist`, `Show`, `ShowDate`, `Ticket`, `Venue`
- `SampleData.kt` — hardcoded mock artists, tickets, show dates
- Color scheme: dark navy/purple (`#1A1A2E`, `#7B2FBE`) — did NOT match the web app

Nothing in the skeleton connected to any real API or database.

---

## Web App Reference (what the mobile app should be inspired by)

**Backend base URL:**
- Live: `adamdistanti.xyz`
- Local dev: backend runs on port `5001` (Vite frontend is on `5173`, but mobile hits the backend directly)

**Account types:** `member` and `fan`

**Key API endpoints already built:**
- `POST /api/auth/register`
- `POST /api/auth/login`
- `GET /api/auth/verify` (email verification link)
- `GET /api/auth/register-status` (polling delivery status)
- `GET /api/auth/me`
- `PATCH /api/auth/me`
- `POST /api/org/create`, `GET /api/org/me`
- `POST /api/org/invites`, `GET /api/org/invites`, `POST /api/org/invites/accept`
- `GET /api/organizations/:id`
- `GET /api/bookings`, `GET /api/bookings/mine`
- `GET /api/events`, `GET /api/events/:id`
- `POST /api/events`, `PATCH /api/events/:id`, `DELETE /api/events/:id`
- `GET /api/invites`, `POST /api/invites/:id/accept`, `POST /api/invites/:id/decline`

**Not yet built (needed for mobile):**
- `POST /api/events/:id/attend` — mark user as attending an event
- `DELETE /api/events/:id/attend` — unmark attendance
- `GET /api/events/attending` — list events the current user is attending

**Garage rules:**
- Garages: A, B, C, D, H, I
- 4 floors each (1 = ground, 4 = rooftop/open-air)
- Booking window: 6:00 PM – 12:00 AM, 30-min slots
- Max 2 groups per floor per timeslot

**Theme:** UCF black / gold (`#FFC904`) / white

**Password policy:** min 8 chars, 1 uppercase, 1 number, 1 special symbol

**Invite expiry:** 7 days

**Email verification:** Required before login is allowed (24-hour TTL, auto-resends on expiry)
