# Engineering Guidelines

## 1) Architecture
- Keep frontend and backend concerns separated.
- Use modular structure: routes, controllers/services, models, shared utils.
- Avoid putting business logic directly in UI components.

## 2) Reusability
- Reuse shared UI components for repeated layouts/patterns.
- Reuse shared request helpers and validation utilities.
- Prefer configuration/constants over hardcoded values.

## 3) Data and API
- Define request/response contracts before implementation.
- Validate all input server-side.
- Return consistent error formats.
- Enforce role permissions at API layer.

## 4) Git workflow
- Do not push untested features to `main`.
- Use feature branches for each task.
- Keep commits small and focused.
- Reference task IDs in commit messages.

## 5) Testing policy
- Minimum for each task:
  - unit tests for core logic
  - integration tests for critical API flows
  - end-to-end path for user-critical journeys
- Fix failing tests before merge.

## 6) Security and privacy
- Never commit secrets.
- Keep environment variables in `.env` (local/server) and GitHub secrets.
- Apply least-privilege authorization by role.

## 7) Performance and reliability
- Add indexes for frequently queried fields.
- Prevent double-booking race conditions in claim logic.
- Log errors with enough context for debugging.

## 8) Documentation
- Update vault notes whenever requirements or behavior changes.
- Keep API route docs current.
- Keep data model docs current.
