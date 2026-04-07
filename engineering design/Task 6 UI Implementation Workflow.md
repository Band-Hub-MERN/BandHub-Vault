# Task 6 UI Implementation Workflow

## Objective
Implement the Figma overhaul in controlled slices so design quality improves without breaking Task 2 functionality.

## Source of truth
1. Figma design files (and imported source code folder in workspace)
2. Existing Task 2 behavior contracts (auth/session/org/invite)
3. Vault backlog and acceptance criteria

## Working method
- Implement one page slice at a time.
- Keep API contracts stable during visual slices.
- Validate build + smoke test after every slice.
- Commit small, reviewable increments.

## Slice order
1. Home page (current sprint)
2. Auth screens (login/register/verify)
3. Organization + invite management screens
4. Shared components extraction pass
5. Responsive + accessibility polish pass

## Home page slice checklist
- [ ] Compare Figma frame with current `HomePage` structure.
- [ ] Implement layout skeleton (nav/header/main/right panel).
- [ ] Apply Figma token mapping (colors/type/spacing/radius/shadows).
- [ ] Preserve role-aware behavior (`fan` vs `member`).
- [ ] Keep existing API wiring unchanged.
- [ ] Run frontend build.
- [ ] Manual smoke test:
  - [ ] login -> home
  - [ ] fan view renders
  - [ ] member view renders
  - [ ] org prompt still works

## Definition of done for each UI slice
- Visual parity with Figma for that slice.
- No regressions in existing functionality.
- `npm run build` passes.
- Notes updated in vault backlog.

## Branching and PR approach
- Branch: `feature/task-6-figma-ui-overhaul`
- Optional sub-branches per slice if needed:
  - `feature/task-6-1-home-ui`
  - `feature/task-6-2-auth-ui`
- Prefer one PR per slice to keep reviews focused.
