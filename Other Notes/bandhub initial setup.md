# Campus Parking Garage Band Tracker — Initial Obsidian Setup

This note is the initial Obsidian setup for the **Campus Parking Garage Band Tracker** project.

It is written to support a **task-first workflow** and to be used with **Codex in VS Code**.

---

# Folder Structure

```text
product/
UX/
engineering design/
prompts/
Other Notes/
templates/
attachments/
```

---

# product/

## Suggested notes
- `Task Backlog.md`
- `Feature Scope.md`
- `MVP Definition.md`
- `Role Permissions.md`
- `Authentication and Registration.md`

## Core product concept
The app has two major product areas:

1. **Garage practice spot claiming** for bands and organizations
2. **Event discovery** for fan accounts

## Role rules
### Fan accounts
- can register on **web and mobile**
- can only access **posted events**
- **cannot** see where bands are practicing
- on the shared home page, when a fan clicks a garage, the fan sees a filled event calendar view for that garage
- fans do **not** see practice bookings

### Band accounts
- can only be registered on the **web app**
- can find and claim practice spots in a time slot
- can create events
- can view both **events** and **practice bookings** when viewing a garage
- band editing and show/event editing are done on the **website**

### Other organization accounts
- should behave similarly to band accounts for garage claiming
- registration is separate from fan registration

## Registration flow
### Shared registration fields
- email
- password
- confirm password

### Registration type choice
On the registration flow, ask the user whether they are registering as:
- band
- fan
- other organization

### Band registration fields
- band name
- member names
- member roles
- plus-sign add button to add another member line object

### Other organization registration fields
- organization name

### Fan registration fields
- name

## Website flow
1. Start on the login page
2. User logs in with email and password
3. User can register from the first-page flow
4. After registration/login, user goes to the home page

## Simplified domain language
- Use **Event** only
- Do **not** use a separate Venue concept for now
- An **Event** means a small fan-facing show/performance that a band posts
- Practice bookings are separate from events

## Practice spot rule
- A **practice spot** is one **entire garage floor**
- Limit to **2 bands/organizations per garage at a time**
- Example: one group on the top floor and one group on the bottom floor

## Task-first structure
### Task 1 — Initial setup and API baseline
Goal:
- create the initial app skeleton
- confirm the database connection works
- confirm the API server works
- confirm frontend can talk to backend
- create initial setup files and route/model skeletons
- adapt the starter MERN assignment structure into the new domain

Done when:
- backend starts cleanly
- database connects cleanly
- at least one API endpoint works
- frontend successfully calls backend
- base folder structure is ready for feature work

### Task 2 — Authentication and registration
Goal:
- make the login page work first
- implement authentication and registration flow
- support role selection during registration
- support the required fields for fan, band, and other organization registration

Done when:
- user can register correctly by role
- user can log in with email and password
- user is routed to the home page after successful login

---

# UX/

## Suggested notes
- `Login and Registration Flow.md`
- `Shared Home Page Layout.md`
- `Role-Based Garage View.md`
- `Garage Claim Flow.md`
- `Event Discovery Flow.md`
- `UCF Theme Notes.md`

## Seeded UX direction
- The web app comes first
- Web and mobile are **separate apps**
- Do **not** use built-in React tools to auto-generate the web app from the mobile app
- The event side should feel similar in spirit to **Ticketmaster**, but with **UCF-themed colors**
- Figma is already being used for planning layouts and structure
- Figma is connected to VS Code + Codex, so design context can be referenced during development

## Shared home page rule
Use the **same overall home page structure** for all user types.

However, when a user clicks a garage:
- **Fan view**: sees only event information for that garage
- **Band / other organization view**: sees event information **and** practice bookings for that garage

## Current home page draft ideas
Based on the rough draft direction so far:
- shared greeting area
- list of garages in the main content area
- booking/details panel on the right
- leaderboard area near the bottom
- left navigation sidebar

## Design directions to explore
- black / gold / white UCF-inspired palette
- event cards with a ticketing-style layout
- simple calendar visibility per garage
- clean separation between practice data and fan-visible event data

---

# engineering design/

## Suggested notes
- `MERN Starter Refactor Plan.md`
- `Task 1 Technical Checklist.md`
- `API Routes.md`
- `Mongo Models.md`
- `Authentication Notes.md`

## Technical baseline
This project builds on the provided MERN starter setup.

Use that starter as the basis for:
- frontend structure
- backend structure
- initial API pattern
- database setup pattern
- future auth pattern

## Platform plan
- **Web app first**
- mobile app later
- web and mobile should be treated as separate implementations

## Early core entities
Potential initial models:
- `User`
- `BandProfile`
- `OrganizationProfile`
- `Garage`
- `GarageFloor`
- `PracticeClaim`
- `Event`

## Early route areas
- auth routes
- registration routes
- garage routes
- event routes

## Task 1 technical focus
Task 1 should prove:
- Express app starts
- MongoDB connection works
- test route responds
- frontend can hit backend successfully
- starter files are reorganized around the new domain

## Task 2 technical focus
Task 2 should implement:
- login endpoint
- registration endpoint(s)
- role handling
- field validation
- frontend login page
- frontend registration flow
- post-login redirect to home page

---

# prompts/

## Suggested notes
- `Codex Session 1.md`
- `Task 1 Prompts.md`
- `Task 2 Prompts.md`
- `Refactor Prompts.md`

## Prompt directions
### Task 1 prompts should focus on
- analyzing the baseball-card MERN starter
- renaming and refactoring it into the parking garage band tracker domain
- generating initial route/model/file skeletons
- proving DB + API + frontend connection works

### Task 2 prompts should focus on
- implementing auth and registration
- role-based registration fields
- login page flow
- registration page flow
- redirect to home page

---

# Other Notes/

## Suggested notes
- `Questions to Resolve.md`
- `Random Ideas.md`
- `Future Mobile Notes.md`

## Current unresolved areas
- exact home page content for each role after login
- how leaderboard should work
- how event calendar display should work per garage
- exact data model for band members and roles
- what comes after Task 2

---

# templates/

## Daily Dev Log
```md
# {{date:YYYY-MM-DD}} — Dev Log

## Built Today
- 

## Decisions
- 

## Problems
- 

## Next Step
- 
```

## Task Template
```md
# Task {{title}}

## Goal

## Scope
- 

## Done When
- 

## Dependencies
- 
```

## Feature Template
```md
# {{title}}

## User Story

## Requirements
- 

## Notes
- 
```

---

# attachments/

Use this folder for:
- Figma screenshots
- UCF color references
- Ticketmaster inspiration screenshots
- whiteboard diagrams
- wireframes

---

# Current status
- Task 1 (MERN baseline from professor guide): **Completed**
- Next active task: **Task 2 — Authentication and registration**

The next planning step after this note is to define:
1. exact post-login home page content by role
2. Task 2 auth + registration flow in more detail
3. garage-claiming constraints and booking rules in final detail

See also:
- `product/Task Backlog.md`
- `engineering design/Definition of Done.md`
- `engineering design/Engineering Guidelines.md`
- `Other Notes/Questions to Resolve.md`
