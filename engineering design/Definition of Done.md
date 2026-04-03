# Definition of Done (DoD)

A task is complete only when all of the following are true:

1. Scope met
- All acceptance criteria in the task note are satisfied.
- No incomplete placeholders remain.

2. Code quality met
- Code is modular and reusable.
- Repeated UI is extracted into reusable components.
- Repeated backend logic is extracted into shared utilities/services.
- No dead code or debug-only artifacts left.

3. Validation met
- Feature works locally end-to-end.
- Error cases are handled and tested.
- Existing behavior is not regressed.

4. Testing met
- Required tests for the task are added/updated.
- Test suite for impacted areas passes.
- Manual verification checklist is completed.

5. Review readiness met
- Files are organized and named clearly.
- Task note updated with final decisions.
- Known tradeoffs/limitations documented.

6. Integration met
- CI checks pass.
- Deployment impact reviewed.
- Safe rollback path is known for risky changes.
