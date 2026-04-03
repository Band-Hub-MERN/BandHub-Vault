# Task 2 Implementation Plan (Draft)

## Objective
Deliver production-ready MVP authentication + registration for two account types:
- `fan`
- `member`

Then allow member users to create one organization profile and invite other members.

## Confirmed product decisions (as of 2026-04-02)
- One account type per email.
- Password reset deferred to later task.
- Account-type registration required.
- UCF visual direction (black/gold/white) for UI.
- Password policy: min 8 chars, at least 1 uppercase, 1 number, 1 symbol.
- Organization category/type is required.
- Band member roles are free-text.
- Web app first; mobile app is separate and will be built later in Android Studio.
- Invite expiry window: 7 days.
- Organization owner assigns granular permissions to members.
- Member with no organization is prompted (not forced) to create/join.
- Fan home default shows upcoming campus-wide events, grouped by garage then date/time.

## Recommendation for org-member management

### Chosen approach
Use separate member logins linked to one organization.

Why this is best:
- Better security (no shared org password)
- Better auditability (who changed what)
- Better scalability for roles and permissions

## Proposed data model for Task 2

### User
- `_id`
- `email` (unique)
- `passwordHash`
- `accountType` (`fan` | `member`)
- `displayName`
- `organizationId` (nullable, required for members after onboarding)
- `memberRoleLabel` (free-text, optional for non-band orgs)
- `createdAt`
- `updatedAt`

### OrganizationProfile
- `_id`
- `ownerUserId`
- `name`
- `category` (free-text, examples: band, dance club, frat, etc.)
- `members[]` (user ids)
- `invites[]` (pending invite objects)
- `createdAt`

### OrganizationMemberPermission
- `canInviteMembers`
- `canRemoveMembers`
- `canEditOrganization`
- `canDeleteOrganization`
- `canManagePermissions`

### OrganizationInvite
- `_id`
- `organizationId`
- `invitedEmail`
- `status` (`pending` | `accepted` | `expired` | `revoked`)
- `expiresAt`
- `createdBy`
- `createdAt`

### FanProfile
- `_id`
- `userId`
- `name`
- `createdAt`

## API endpoints (Task 2)
- Auth:
  - `POST /api/auth/register`
  - `POST /api/auth/login`
  - `GET /api/auth/me`
  - `POST /api/auth/logout` (optional)
- Organization onboarding:
  - `POST /api/org/create`
  - `GET /api/org/me`
- Invite flow:
  - `POST /api/org/invites`
  - `POST /api/org/invites/accept`
  - `GET /api/org/invites`

## Validation baseline
- Email must be unique globally.
- `accountType` required and immutable in MVP.
- Password must satisfy policy:
  - length >= 8
  - at least 1 uppercase letter
  - at least 1 number
  - at least 1 special symbol
- Account-type fields required:
  - fan: `name`
  - member: `displayName`
- Organization fields required for create:
  - `name`
  - `category`

## Frontend pages/components (Task 2)
- Login page
- Register page with account-type selector (`fan` or `member`)
- Account-type registration sections
- Organization creation step for first-time member login
- Non-blocking org prompt modal/card (join/create now or later)
- Invite management UI (send invite, pending list, accept invite flow)
- Permission management UI for owner (and any member with manage-permissions permission)
- Auth gate and post-login redirect to home

## Tests required for Task 2
- Unit: validators and account-type routing logic
- Integration: register/login/auth-me + org create + invite accept
- E2E:
  - fan register -> login -> home
  - member register -> create org -> send invite
  - invited member accept -> join org

## Exit criteria
- Member and fan registrations function correctly.
- Login works with persisted auth state.
- Member can create organization profile.
- Member invite workflow functions end-to-end.
- Invite expiry enforced at 7 days.
- Permission checks enforce org actions correctly.
- Correct home redirect occurs by account type.
- CI passes and no failing tests.

## Open blockers for finalizing this plan
1. Invite resend policy details (manual resend vs cooldown).
2. Initial default permission template for newly invited members.
