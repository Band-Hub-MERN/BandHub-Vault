# Mobile Definition of Done

Mirrors the web app Definition of Done, adapted for Flutter.

A mobile task is complete only when ALL of the following are true:

---

## 1) Scope Met
- All acceptance criteria listed in the task's roadmap section are satisfied.
- No placeholder `// TODO` comments remain in submitted code.
- No hardcoded mock data is left active (unless the task explicitly allows it during early scaffolding).

## 2) Code Quality Met
- Follows all rules in `Mobile Engineering Guidelines.md`.
- No business logic inside `build()` methods.
- No raw `Map<String, dynamic>` objects passed between layers — typed models only.
- Shared UI patterns extracted into `widgets/`. No duplicated widget code across screens.
- Theme colors and text styles referenced from `lib/theme/` — no inline hex values or hardcoded sizes.
- `flutter analyze` returns zero warnings.

## 3) Validation Met
- Feature works end-to-end on Android emulator against the live API (`adamdistanti.xyz`).
- All error states are handled: network failure, 401 unauthorized, 404 not found, 400 validation error.
- Loading states are shown while data is fetching (shimmer or spinner).
- Empty states are shown when lists return no results.
- Auth guard prevents unauthenticated access to protected screens.

## 4) Testing Met
- Unit tests written for any new service method or provider logic.
- Widget test written for any new shared widget added to `widgets/`.
- Existing tests still pass (`flutter test`).
- No live API calls in tests — mocked responses only.

## 5) Review Readiness Met
- Code is organized per the folder structure defined in the roadmap.
- Branch is named correctly: `mobile/task-X-description`.
- Commit messages reference the task number.
- `.env` is not committed.
- PR description states what changed, how it was tested, and includes a screenshot or screen recording of the UI on the emulator.

## 6) Integration Met
- Mobile changes that require new backend endpoints confirm those endpoints are deployed and working before the mobile task is marked done.
- No mobile task is merged to `main` if it depends on a backend change that is still on a feature branch.
- If the task touches the backend (`api.js` or models), the backend task is also marked done first.
