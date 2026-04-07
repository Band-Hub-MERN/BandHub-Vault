# Task Backlog

## Status Key
- Planned
- In Progress
- Blocked
- Done

## Tasks

### Task 1 — MERN Baseline from Professor Guide
- Status: Done
- Goal: Prove end-to-end MERN flow, deployment, and CI/CD baseline.
- Done Evidence:
  - frontend and backend run
  - MongoDB connection works
  - deployed app works
  - CI/CD pipeline works

### Task 2 — Authentication, Registration, and Organization Onboarding
- Status: Done
- Goal: Implement complete login + registration flow, support member/fan account types, and route user to home page.
- Scope:
  - email/password login
  - exactly one account type per email
  - registration type selection (`member`, `fan`)
  - member account can create one organization profile
  - organization profile includes `name` and `category/type` (band, club, frat, etc.)
  - invite flow for additional member accounts to join organization
  - server-side validation
  - password reset deferred (not in MVP for Task 2)
  - post-login redirect to home
- Done When:
  - member and fan registrations succeed with validation
  - login works for registered users
  - organization creation works for member accounts
  - member invite flow works end-to-end
  - user reaches home page after auth
  - tests pass for auth endpoints and UI flow

- Done Evidence (This Branch):
  - member/fan registration and login flows implemented and wired to backend
  - email verification page + token verification flow implemented
  - registration includes confirm-password validation
  - invite inbox with accept/decline implemented
  - organization create/join/invite/remove/role-update flows implemented
  - profile save includes bio persistence fix
  - post-login routing to dashboard flow is working

#### Task 2 Breakdown (Execution Order)

##### Task 2.1 — Backend Auth Foundation + Core Models
- Status: Done
- Scope:
  - create/confirm core models (`User`, `OrganizationProfile`, `OrganizationInvite`)
  - add password policy validator (8+ chars, uppercase, number, symbol)
  - define permission schema for org members
  - add shared auth/validation utilities
- Done When:
  - models exist and are wired
  - validation utilities are callable in endpoints

##### Task 2.2 — Auth API Endpoints
- Status: Done
- Scope:
  - implement `POST /api/auth/register`
  - implement `POST /api/auth/login`
  - implement `GET /api/auth/me`
  - enforce one account type per email
- Done When:
  - register/login/me work end-to-end with JWT session behavior

##### Task 2.3 — Organization Create/Join/Invite APIs
- Status: Done
- Scope:
  - implement org create endpoint for member accounts
  - implement invite creation/list endpoints
  - implement invite accept endpoint
  - enforce 7-day invite expiry
- Done When:
  - member can create org and invite another member who can join successfully

##### Task 2.4 — Frontend Auth and Onboarding UX
- Status: Done
- Scope:
  - login page and registration page updates for `member`/`fan`
  - non-blocking prompt for members without org (join/create)
  - fan default home feed: events grouped by garage, then date/time
- Done When:
  - user can complete auth and land on correct post-login view

##### Task 2.5 — Permission Management
- Status: Done
- Scope:
  - implement permission update endpoint
  - enforce permissions for invite/remove/edit/delete actions
  - add minimal UI to view/update member permissions
- Done When:
  - permission-restricted actions are blocked/allowed correctly

##### Task 2.6 — Testing + CI Gate for Task 2
- Status: In Progress
- Scope:
  - unit tests for password + permission + invite expiry logic
  - integration tests for auth and invite flows
  - E2E smoke path for fan and member journeys
- Done When:
  - Task 2 tests pass in CI before merge

### Task 3 — Garage Claiming Core
- Status: In Progress
- Goal: Enable organizations to claim practice slots per garage floor.
- Constraint:
  - garages: A, B, C, D, H, I
  - 4 floors per garage
  - max 2 groups per floor per timeslot
  - custom booking start/end time
  - allowed booking window: 6:00 PM to 12:00 AM
  - claim horizon: up to 3 months out
  - one slot per organization per day (cannot hold multiple claims on same day)
  - repeating weekly bookings allowed
  - repeating daily bookings not allowed

- Done Evidence (This Branch):
  - garage booking UI implemented with floor/time slot selection
  - booking group selector uses real organization memberships
  - no-organization member state handled with gated booking action
  - booking cancel actions implemented with in-app confirmation modal
  - dashboard + garages pages show/manage member bookings
  - booking availability now reflects both bookings and events for shared capacity
  - max 2 groups per floor/time conflict handling enforced in booking/event flows

### Task 4 — Event Discovery for Fans
- Status: Done
- Goal: Fans can see events only (no practice claim visibility).
- Constraint:
  - events are independent from practice claims
  - practice claims are visible only to organization account types

- Done Evidence (This Branch):
  - events feed and event details pages implemented
  - fan-safe dashboard/garage view hides private practice details
  - member-only event management actions (create/edit/delete) implemented
  - event editing now updates existing records (no duplicate create on edit)
  - event delete uses in-app confirmation modal

### Task 5 — Testing Repo Integration
- Status: Planned
- Goal: Link and enforce unit/integration/E2E workflows before production merges.

### Task 6 — Figma UI Overhaul (Web App)
- Status: In Progress
- Goal: Implement the approved Figma design system and layouts across the BandHub web app.
- Scope:
  - apply Figma design tokens (color, typography, spacing, radius, shadows)
  - replace current MVP styling with production-grade UI components
  - implement shared app shell/navigation from Figma
  - refactor auth, home, and organization screens to match Figma
  - keep existing Task 2 auth/org functionality working while redesigning UI
  - desktop-first responsive implementation
- Done When:
  - key screens match Figma at high fidelity
  - reusable UI component primitives are in place
  - no auth/org flow regressions introduced by styling changes
  - frontend build and smoke tests pass

#### Task 6 Workflow (Page-by-Page)

##### Task 6.1 — Home Page UI Slice (First)
- Status: Done
- Scope:
  - map imported Figma home layout to current React structure
  - implement visual shell: header, sidebar/nav, content columns, right detail panel
  - preserve current member/fan conditional behavior while replacing layout and styling
  - do not change backend contracts in this slice
- Done When:
  - home page matches Figma structure and style at high fidelity
  - fan and member views still load correctly
  - build passes and smoke test passes

##### Task 6.2 — Auth Screens UI Slice
- Status: Done
- Scope:
  - apply Figma styling/layout to login/register/verify screens
  - keep existing Task 2 auth functionality unchanged

##### Task 6.3 — Organization/Invite UI Slice
- Status: Done
- Scope:
  - apply Figma components to org creation, invite form/list, and permission panels

##### Task 6.4 — Shared Component System
- Status: Done
- Scope:
  - extract reusable Figma-based primitives (buttons, inputs, cards, chips, tabs, panels)
  - refactor duplicated UI into shared components

##### Task 6.5 — Responsive Pass + Polish
- Status: In Progress
- Scope:
  - desktop-first refinement
  - tablet/mobile web fallback behavior and spacing adjustments
  - accessibility and contrast polish

---

## Branch Completion Snapshot (Current Branch)

- ✅ Multi-organization membership support across org, booking, and event flows
- ✅ Organization selection added to both booking creation and event creation
- ✅ Unified capacity model: events and bookings share the same garage/floor/time limits
- ✅ Event edit route + update behavior fixed (`/events/:id/edit`)
- ✅ In-app confirmation modal component introduced and adopted for destructive actions
- ✅ Booking cancel and event delete confirmation moved from browser confirm to in-app modal
- ✅ Organization delete flow completed for owners (frontend + backend cascade behavior)
- ✅ Profile bio persistence fixed
- ✅ Test-only member/fan toggle removed from authenticated UI
- ✅ Frontend lint stabilized (errors resolved; warnings tracked)

## Next Recommended Follow-ups

- Complete Task 2.6 test coverage and CI gates (unit/integration/E2E)
- Finish Task 6.5 responsive + accessibility polish pass
- Optionally reduce remaining lint warnings (`any` typing + exhaustive deps)
