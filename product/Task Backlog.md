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
- Status: Planned
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

#### Task 2 Breakdown (Execution Order)

##### Task 2.1 — Backend Auth Foundation + Core Models
- Status: Planned
- Scope:
  - create/confirm core models (`User`, `OrganizationProfile`, `OrganizationInvite`)
  - add password policy validator (8+ chars, uppercase, number, symbol)
  - define permission schema for org members
  - add shared auth/validation utilities
- Done When:
  - models exist and are wired
  - validation utilities are callable in endpoints

##### Task 2.2 — Auth API Endpoints
- Status: Planned
- Scope:
  - implement `POST /api/auth/register`
  - implement `POST /api/auth/login`
  - implement `GET /api/auth/me`
  - enforce one account type per email
- Done When:
  - register/login/me work end-to-end with JWT session behavior

##### Task 2.3 — Organization Create/Join/Invite APIs
- Status: Planned
- Scope:
  - implement org create endpoint for member accounts
  - implement invite creation/list endpoints
  - implement invite accept endpoint
  - enforce 7-day invite expiry
- Done When:
  - member can create org and invite another member who can join successfully

##### Task 2.4 — Frontend Auth and Onboarding UX
- Status: Planned
- Scope:
  - login page and registration page updates for `member`/`fan`
  - non-blocking prompt for members without org (join/create)
  - fan default home feed: events grouped by garage, then date/time
- Done When:
  - user can complete auth and land on correct post-login view

##### Task 2.5 — Permission Management
- Status: Planned
- Scope:
  - implement permission update endpoint
  - enforce permissions for invite/remove/edit/delete actions
  - add minimal UI to view/update member permissions
- Done When:
  - permission-restricted actions are blocked/allowed correctly

##### Task 2.6 — Testing + CI Gate for Task 2
- Status: Planned
- Scope:
  - unit tests for password + permission + invite expiry logic
  - integration tests for auth and invite flows
  - E2E smoke path for fan and member journeys
- Done When:
  - Task 2 tests pass in CI before merge

### Task 3 — Garage Claiming Core
- Status: Planned
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

### Task 4 — Event Discovery for Fans
- Status: Planned
- Goal: Fans can see events only (no practice claim visibility).
- Constraint:
  - events are independent from practice claims
  - practice claims are visible only to organization account types

### Task 5 — Testing Repo Integration
- Status: Planned
- Goal: Link and enforce unit/integration/E2E workflows before production merges.
