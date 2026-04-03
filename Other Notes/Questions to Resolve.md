# Questions to Resolve — Product Discovery

## A) Auth and account model
1. Is one email allowed to create multiple account types (`fan` and `organization`), or exactly one?
2. For organization accounts, can multiple members manage the same organization profile?
3. Do you want email verification in MVP, or skip until later?
4. Password reset needed in MVP?

## B) Registration fields and validation
5. For `band`, are member roles free-text or a fixed list?
6. For `band`, do member names need uniqueness within the band?
7. For `other organization`, do you need category type (dance, frat, club, etc.)?
8. What are required password rules (min length, complexity)?

## C) Garage claiming rules
9. What is the booking unit: 30 min, 60 min, custom start/end?
10. Earliest/latest allowed booking times?
11. How far in advance can a group claim?
12. Can claims repeat weekly, or single-instance only for MVP?
13. Can a group hold multiple active claims at once?
14. Do admins exist to override conflicts/cancel claims?

## D) Garage structure
15. Confirm garages are fixed to A, B, C, D, H, I for MVP.
16. Confirm each garage has exactly 4 floors for MVP.
17. Should floor names be numeric (`1-4`) or domain labels (`top`, `bottom`, etc.)?

## E) Visibility and permissions
18. Confirm fans never see practice claims in UI or API.
19. Can fans see organization profile pages, or only events?
20. Can organizations see other organizations’ claims?

## F) Event model
21. What fields are required for an event (title, description, date/time, garage, floor, host, ticket info)?
22. Are events always tied to one garage/floor?
23. Should an event require an existing practice claim, or independent?
24. Event approval/moderation needed for MVP?

## G) Home screen behavior
25. What is the exact first screen after login for each role?
26. What should the right-side panel default to on first load?
27. What should leaderboard measure (most events, most attendance, most active org)?

## H) UX + brand
28. Confirm official UCF palette values (hex codes) to use.
29. Preferred typography style?
30. Mobile responsiveness level required for MVP web app?

## I) Non-functional and release
31. Target launch milestone/date?
32. Team roles and review owner for merge approvals?
33. Required test coverage minimum before merge?
34. Any legal/safety disclaimer needed for garage usage?

---

## Decision Log
Use this section to record final answers and dates.

### Resolved (2026-04-02)
- Q1: One role per account/email.
- Q2: Multiple members can manage one organization profile.
- Q4: Password reset is not required for MVP Task 2.
- Q5: Band member roles are free-text.
- Q7: Organization category/type is required.
- Q8: Password policy: minimum 8 chars, at least 1 uppercase, 1 number, 1 special symbol.
- Q9: Booking uses custom start/end time.
- Q10: Booking time window is 6:00 PM to 12:00 AM.
- Q11: Booking horizon is up to 3 months out.
- Q12: Repeat weekly bookings are allowed; repeat daily bookings are not.
- Q13: Organizations can hold multiple claims only if they are on different days (max one per day).
- Q15: Garages fixed for MVP: A, B, C, D, H, I.
- Q16: Each garage has exactly 4 floors for MVP.
- Q17: Floor naming is numeric 1-4 (1=ground, 4=top/open-air).
- Q18: Fans must not see practice claims.
- Q23: Events are independent from practice claims.
- Q27: Leaderboard is postponed.
- Q28: Theme direction confirmed: UCF black / gold / white.
- Q30: Web-first development confirmed; mobile app is separate later project.
- Q34: No legal/safety disclaimer needed in MVP yet.
- Invite expiry window: 7 days.
- Permission model: organization creator grants granular permissions (invite/remove/edit/delete/manage-permissions).
- Member without org: show non-blocking prompt to join/create; do not force.
- Fan home default: upcoming events campus-wide, sorted/grouped by garage then date/time.

### Remaining High-Priority Open Questions
1. Q14: Admin override/cancel capability needed in MVP?
2. Q19/Q20: Exact visibility of organization profiles and other organizations’ claims.
3. Q21/Q22/Q24: Event required fields, location coupling, moderation.
4. Q29: Typography preference.
5. Q31/Q33: Target release date and minimum test threshold before merge.
6. Invite resend policy and default permission template for invited members.

### New Clarification Added
- Account model update: Use two account types only:
  - `member` (can create/manage organization and invites)
  - `fan` (can only view public events)
