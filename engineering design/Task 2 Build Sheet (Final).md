# Task 2 Build Sheet (Final)

## Scope
Implement authentication, registration, organization onboarding, and invite/permission management for web app MVP.

## Account Model
- `fan`
- `member`

Rules:
- One account type per email.
- Password reset deferred.

## Security and Password Policy
Password must include:
- minimum 8 characters
- at least 1 uppercase letter
- at least 1 number
- at least 1 special symbol

## Organization Model (for member accounts)
A member can create or join an organization.

Organization fields:
- `name`
- `category` (free text: band, club, frat, etc.)

Member permissions (granular):
- `canInviteMembers`
- `canRemoveMembers`
- `canEditOrganization`
- `canDeleteOrganization`
- `canManagePermissions`

Invite rules:
- invite expires in 7 days

## UX Rules
- Web app first.
- Mobile app is separate future project in Android Studio.
- Member with no org is shown a non-blocking join/create prompt.
- Fan home default shows upcoming campus-wide events grouped by garage, then date/time.

## Task 2 API Set
Auth:
- `POST /api/auth/register`
- `POST /api/auth/login`
- `GET /api/auth/me`

Organization onboarding:
- `POST /api/org/create`
- `GET /api/org/me`

Invites and permissions:
- `POST /api/org/invites`
- `GET /api/org/invites`
- `POST /api/org/invites/accept`
- `PATCH /api/org/members/:memberId/permissions`
- `DELETE /api/org/members/:memberId`

## Frontend Views (Task 2)
1. Login page
2. Register page (fan/member selector)
3. Member onboarding prompt (non-blocking)
4. Create organization form
5. Join organization flow (via invite)
6. Invite management panel
7. Permission management panel
8. Fan home events feed (garage-grouped)

## Validation Rules (minimum)
- Unique email
- Required fields by account type
- Permission checks on all organization-management endpoints
- Invite must be valid, pending, and unexpired

## Tests Required Before Merge
Unit:
- password validator
- permission guard logic
- invite expiry checker

Integration:
- register/login/me flow
- create organization
- invite + accept flow
- permission update + enforcement

E2E:
- fan register/login -> home events feed
- member register/login -> create org
- member invite -> invited member accept -> appears in org

## Done Criteria
Task 2 is done when:
- account registration/login is stable for `fan` and `member`
- member can create org and invite users
- org permission controls are enforced
- fan landing feed behavior matches product decision
- tests pass and CI is green

## Deferred (Not in Task 2)
- password reset
- mobile app implementation
- leaderboard
- legal/safety disclaimer
