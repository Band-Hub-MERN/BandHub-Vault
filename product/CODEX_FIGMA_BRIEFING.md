# Garage Jam — Codex Integration Briefing

This document gives you everything you need to integrate the Figma Make frontend
into the Garage Jam monorepo backend. Read it fully before touching any file.

---

## 1. What this frontend is

Garage Jam is a UCF student garage practice-space and event-planning platform.
The downloaded Figma Make folder contains a **complete, production-quality React
frontend**. Your job is to wire it to the real backend — replacing mock data and
stub auth with real API calls — without redesigning anything.

---

## 2. Tech stack (frontend)

| Concern | Library |
|---|---|
| Framework | React 18 + TypeScript |
| Bundler | Vite 6 |
| Routing | `react-router` v7 (Data mode — `createBrowserRouter`) |
| Styling | Tailwind CSS v4 + CSS custom properties |
| Toasts | `sonner` |
| Icons | `lucide-react` |
| Animation | `motion` (import from `'motion/react'`) |
| Forms | `react-hook-form@7.55.0` |
| UI primitives | Radix UI (all shadcn-style components in `src/app/components/ui/`) |

Do **not** swap any of these out. Do **not** add `react-router-dom` — only
`react-router` is used here.

---

## 3. Folder structure of the Figma export

```
src/
├── app/
│   ├── App.tsx                  ← root, wraps RouterProvider in AppProvider
│   ├── routes.tsx               ← all routes defined here
│   ├── context/
│   │   └── AppContext.tsx       ← global auth + user state (REPLACE WITH REAL AUTH)
│   ├── data/
│   │   └── mockData.ts          ← ALL mock data + TypeScript types (REPLACE WITH API)
│   ├── pages/
│   │   ├── Landing.tsx          ← public marketing/splash page
│   │   ├── Login.tsx            ← login form
│   │   ├── Register.tsx         ← registration form
│   │   ├── EmailVerification.tsx
│   │   ├── MemberDashboard.tsx  ← home screen after login
│   │   ├── GarageBooking.tsx    ← book a practice floor (members only)
│   │   ├── OrganizationManagement.tsx ← create/manage orgs, invite/remove members
│   │   ├── EventsFeed.tsx       ← public events list
│   │   ├── EventCreate.tsx      ← create a new event (members only)
│   │   ├── EventDetails.tsx     ← single event detail view
│   │   ├── Invites.tsx          ← accept/decline org invitations
│   │   └── Settings.tsx         ← user profile + notification prefs
│   └── components/
│       ├── layout/
│       │   └── DashboardLayout.tsx  ← sidebar + header shell for all auth'd pages
│       ├── ui/
│       │   ├── StarField.tsx    ← animated twinkling star background (keep as-is)
│       │   └── *.tsx            ← all shadcn/Radix UI components (keep as-is)
│       └── figma/
│           └── ImageWithFallback.tsx ← wrapper for <img> (keep as-is)
└── styles/
    ├── index.css    ← imports fonts, tailwind, theme
    ├── fonts.css    ← Google Fonts: Inter + Barlow Condensed
    ├── tailwind.css ← Tailwind v4 source directive
    └── theme.css    ← ALL design tokens as CSS custom properties
```

---

## 4. TypeScript types — these ARE your API response schemas

These types live in `src/app/data/mockData.ts`. Your backend must return data
matching these shapes. Do not change the type names; the UI imports them directly.

```typescript
type AccountType = 'member' | 'fan';

interface User {
  id: string;
  name: string;
  email: string;
  avatar: string;          // 2-letter initials, e.g. "AR"
  accountType: AccountType;
  orgId?: string;          // primary org id (legacy compat)
  orgIds?: string[];       // all org ids this user belongs to
}

interface OrgMember {
  id: string;
  name: string;
  email: string;
  avatar: string;          // initials
  role: 'owner' | 'admin' | 'member';
  joinedAt: string;        // ISO date string "YYYY-MM-DD"
}

interface Organization {
  id: string;
  name: string;
  category: string;        // see CATEGORIES constant below
  description: string;
  memberCount: number;
  members: OrgMember[];
  color: string;           // hex color for org branding, e.g. "#FFC904"
  initials: string;        // 2-letter org initials, e.g. "RK"
}

interface Booking {
  id: string;
  garageId: string;        // one of: 'A' | 'B' | 'C' | 'D' | 'H' | 'I'
  floor: number;           // 1 | 2 | 3 | 4
  startTime: string;       // "HH:MM" 24h format, e.g. "18:00"
  endTime: string;         // "HH:MM" 24h format, e.g. "20:00"
  groupName: string;       // display name of the booking org
  orgId: string;
  date: string;            // "YYYY-MM-DD"
  isWeekly: boolean;       // weekly recurring booking
  isOwn?: boolean;         // true if this booking belongs to the current user's org
}

interface GarageEvent {
  id: string;
  title: string;
  orgName: string;
  orgId: string;
  orgColor: string;        // hex
  garageId: string;        // 'A' | 'B' | 'C' | 'D' | 'H' | 'I'
  floor: number;           // 1–4
  date: string;            // "YYYY-MM-DD"
  startTime: string;       // "HH:MM"
  endTime: string;         // "HH:MM"
  description: string;
  category: string;
  coverImage: string;      // full URL to cover image
  attendees: number;       // RSVP/interest count
  isPublic: boolean;       // only public events appear in the fan feed
}

interface Invite {
  id: string;
  orgName: string;
  orgColor: string;        // hex
  sentBy: string;          // display name of person who sent it
  sentAt: string;          // human-readable relative time e.g. "2 hours ago"
  role: string;            // "Member" | "Admin"
}
```

---

## 5. Business constants — single source of truth

These are defined in `mockData.ts` and imported everywhere. Do not hardcode these
values in any page component — always import from `mockData.ts`.

```typescript
// The 6 UCF garages
const GARAGES = ['A', 'B', 'C', 'D', 'H', 'I'] as const;
type GarageId = 'A' | 'B' | 'C' | 'D' | 'H' | 'I';

const GARAGE_NAMES: Record<GarageId, string> = {
  A: 'Garage A', B: 'Garage B', C: 'Garage C',
  D: 'Garage D', H: 'Garage H', I: 'Garage I',
};

const GARAGE_LOCATIONS: Record<GarageId, string> = {
  A: 'Near main campus entrance',
  B: 'Near library and RWC',
  C: 'Near Engineering & Research buildings',
  D: 'Near Memory Mall',
  H: 'Near classroom buildings',
  I: 'Near main Lynx Bus stop & performing arts building',
};

const FLOOR_NAMES: Record<number, string> = {
  1: 'Ground Level', 2: 'Level 2', 3: 'Level 3', 4: 'Rooftop (Open Air)',
};

// Booking time slots — 6 PM to 11:30 PM in 30-min increments (24h strings)
const TIME_SLOTS = [
  '18:00','18:30','19:00','19:30','20:00','20:30',
  '21:00','21:30','22:00','22:30','23:00','23:30',
];

const TIME_LABELS: Record<string, string> = {
  '18:00':'6:00 PM', '18:30':'6:30 PM', '19:00':'7:00 PM', '19:30':'7:30 PM',
  '20:00':'8:00 PM', '20:30':'8:30 PM', '21:00':'9:00 PM', '21:30':'9:30 PM',
  '22:00':'10:00 PM','22:30':'10:30 PM','23:00':'11:00 PM','23:30':'11:30 PM',
};

const CATEGORIES = [
  'Band','Dance','Acapella','DJ','Frat/Sorority','Comedy','Poetry','Other'
];
```

---

## 6. Booking business rules (enforce in both frontend validation AND backend)

- **Hours:** 6:00 PM – 12:00 AM only (slots `18:00`–`23:30`)
- **Capacity:** Max **2 groups per floor** at any overlapping time
- **Advance booking:** Up to **3 months** ahead only
- **Recurrence:** **Weekly repeats only** (no daily, bi-weekly, etc.)
- **Cancellation:** Up to 1 hour before start time
- **Garages:** A, B, C, D, H, I — use plain letter names, no aliases

---

## 7. Routing table

All routes are in `src/app/routes.tsx`. Routes inside `DashboardLayout` are
protected (require auth). The layout renders a sidebar + header with `<Outlet />`.

```
/                   → Landing.tsx          (public)
/login              → Login.tsx            (public)
/register           → Register.tsx         (public)
/verify-email       → EmailVerification.tsx (public)

[inside DashboardLayout — requires auth]:
/dashboard          → MemberDashboard.tsx
/garages            → GarageBooking.tsx    (members only — fans see a lock screen)
/organization       → OrganizationManagement.tsx (members only)
/invites            → Invites.tsx          (members only)
/events             → EventsFeed.tsx       (all users)
/events/create      → EventCreate.tsx      (members only)
/events/:id         → EventDetails.tsx     (all users)
/settings           → Settings.tsx         (all users)
* → redirect to /
```

---

## 8. AppContext — the #1 file to replace

`src/app/context/AppContext.tsx` is currently a stub with hardcoded mock users.
Replace the internals with real auth (JWT, session cookies, Supabase auth, etc.)
**while keeping the same exported interface exactly**:

```typescript
interface AppContextValue {
  user: User | null;           // currently logged-in user
  accountType: AccountType | null;  // 'member' | 'fan' | null
  isLoggedIn: boolean;
  selectedGarage: string | null;
  setSelectedGarage: (g: string | null) => void;
  login: (type: AccountType) => void;   // call after successful auth
  logout: () => void;                    // clears session, redirects to /
  switchAccountType: (type: AccountType) => void; // demo toggle — can remove in prod
}
```

Every page and component imports `useApp()` from this context. The hook throws
if used outside `<AppProvider>`. Do not rename `useApp` or `AppProvider`.

---

## 9. Fan vs Member access rules

This is enforced in the UI. Your backend should mirror the same rules.

| Feature | Member | Fan |
|---|---|---|
| View public events | ✅ | ✅ |
| View org stats / event counts | ✅ | ✅ |
| See campus garage names/locations | ✅ | ✅ |
| See garage occupancy (open/full status) | ✅ | ❌ |
| See floor-level practice session schedules | ✅ | ❌ |
| See which groups are booked when | ✅ | ❌ |
| Book a practice floor | ✅ | ❌ |
| Create/manage organizations | ✅ | ❌ |
| Send/receive org invites | ✅ | ❌ |
| Create events | ✅ | ❌ |
| Access /garages route | ✅ | 🔒 lock screen |

Fans see a `<Lock />` screen on `/garages`. Sidebar hides Garages, Organizations,
and Invites nav items for fan accounts. This is checked via `accountType === 'fan'`
from `useApp()`.

---

## 10. Organization management rules

- A user can belong to **multiple orgs** (`orgIds: string[]`)
- When a user is in 1 org → goes straight to that org's management view
- When a user is in 2+ orgs → shows a list of org cards to pick from
- Roles: `owner` > `admin` > `member`
- Only `owner` can: change roles, delete org
- Only `owner` and `admin` can: invite members, remove members
- Non-owners can **leave** an org (with confirmation). Owners must transfer
  ownership first before leaving.
- Leaving removes the user from the org; they can only rejoin via a new invite.

---

## 11. Key utility functions (keep in mockData.ts or move to a utils file)

```typescript
// Format "2026-04-07" → "Tue, Apr 7"
formatDate(dateStr: string): string

// Format "18:00" → "6:00 PM"
formatTime(timeStr: string): string

// Returns floor occupancy counts for a garage on a given date
// e.g. { 1: 0, 2: 2, 3: 1, 4: 0 }
getGarageOccupancy(garageId: string, date?: string): Record<number, number>
```

When you replace mock data with API calls, move these helpers to
`src/app/utils/formatters.ts` and update imports. `getGarageOccupancy` should
call `GET /api/bookings?garageId=A&date=2026-04-07` and compute the same shape.

---

## 12. API endpoints you need to implement (inferred from UI actions)

### Auth
- `POST /api/auth/login` → `{ user: User, token: string }`
- `POST /api/auth/register` → `{ user: User, token: string }`
- `POST /api/auth/logout`
- `GET  /api/auth/me` → `User`

### Organizations
- `GET  /api/organizations` → `Organization[]` (user's orgs)
- `GET  /api/organizations/:id` → `Organization`
- `POST /api/organizations` → create org
- `PATCH /api/organizations/:id` → update name/category/description
- `DELETE /api/organizations/:id` → owner only
- `POST /api/organizations/:id/invite` → `{ email, role }` → sends invite
- `DELETE /api/organizations/:id/members/:memberId` → remove member
- `PATCH /api/organizations/:id/members/:memberId` → `{ role }` → change role
- `DELETE /api/organizations/:id/leave` → current user leaves

### Invites
- `GET  /api/invites` → `Invite[]` (pending invites for current user)
- `POST /api/invites/:id/accept`
- `POST /api/invites/:id/decline`

### Bookings
- `GET  /api/bookings?garageId=&date=` → `Booking[]`
- `GET  /api/bookings/mine` → `Booking[]` (current user/org's bookings)
- `POST /api/bookings` → `{ garageId, floor, startTime, endTime, date, isWeekly, orgId }`
- `DELETE /api/bookings/:id` → cancel (up to 1h before)

### Events
- `GET  /api/events` → `GarageEvent[]` (public events only for fans; all for members)
- `GET  /api/events/:id` → `GarageEvent`
- `POST /api/events` → create event (members only)
- `PATCH /api/events/:id`
- `DELETE /api/events/:id`

---

## 13. Design system — do not change these

### Colors (use these exact hex values in any new components)
| Token | Hex | Use |
|---|---|---|
| Gold / Primary | `#FFC904` | CTAs, active states, accents |
| Gold hover | `#FFD84D` | Button hover |
| Background | `#09090B` | Page background |
| Surface 1 | `#111113` | Cards |
| Surface 2 | `#18181B` | Popovers |
| Surface 3 | `#1C1C1F` | Inputs, secondary surfaces |
| Surface 4 | `#27272B` | Hover states |
| Muted text | `#8A8A9A` | Labels, subtitles |
| Foreground | `#FAFAFA` | Primary text |
| Success | `#22C55E` |
| Warning / 1 group | `#FFC904` |
| Danger / Full | `#EF4444` |
| Purple (org accent) | `#A855F7` |

### Typography
- **Body / UI:** `Inter` (Google Fonts, weights 300–900)
- **Display headings:** `Barlow Condensed` (weights 600–900), always uppercase
  ```tsx
  style={{ fontFamily: "'Barlow Condensed', sans-serif", fontWeight: 900,
           letterSpacing: '0.02em', textTransform: 'uppercase' }}
  ```

### Spacing & shape
- Border radius: `0.75rem` base (`rounded-xl`), `1rem` for cards (`rounded-2xl`)
- Borders: `border-white/[0.06]` default, `border-white/[0.12]` on hover
- All cards: `bg-[#111113] border border-white/[0.06] rounded-2xl`

### StarField
The animated star background (`src/app/components/ui/StarField.tsx`) is rendered
inside `DashboardLayout` for all authenticated pages. Do not remove it.
It accepts `count` (number of stars) and `className` props.

---

## 14. What to replace vs. what to keep

### REPLACE (stub → real)
| File | What to do |
|---|---|
| `src/app/context/AppContext.tsx` | Swap mock users for real session/JWT auth |
| `src/app/data/mockData.ts` | Replace mock arrays with `fetch`/`axios` hooks; keep types and constants |
| `src/app/pages/Login.tsx` | Wire form submit to `POST /api/auth/login` |
| `src/app/pages/Register.tsx` | Wire form submit to `POST /api/auth/register` |
| `src/app/pages/GarageBooking.tsx` | `handleConfirm` → `POST /api/bookings` |
| `src/app/pages/OrganizationManagement.tsx` | All handlers → real API calls |
| `src/app/pages/Invites.tsx` | `accept`/`decline` → real API calls |
| `src/app/pages/EventCreate.tsx` | Form submit → `POST /api/events` |
| `src/app/pages/EventDetails.tsx` | Load event from `GET /api/events/:id` |
| `src/app/pages/EventsFeed.tsx` | Load events from `GET /api/events` |
| `src/app/pages/MemberDashboard.tsx` | Load bookings, events from real API |
| `src/app/pages/Settings.tsx` | Profile save → `PATCH /api/auth/me` |

### KEEP AS-IS (do not modify)
| File | Reason |
|---|---|
| `src/app/routes.tsx` | Routing structure is complete |
| `src/app/components/layout/DashboardLayout.tsx` | Fully built sidebar + header |
| `src/app/components/ui/StarField.tsx` | Brand visual — do not touch |
| `src/app/components/ui/*.tsx` | All Radix/shadcn components |
| `src/app/components/figma/ImageWithFallback.tsx` | Protected system file |
| `src/styles/theme.css` | All design tokens live here |
| `src/styles/fonts.css` | Font imports |
| `src/app/data/mockData.ts` (types + constants) | Types are the schema contract; `GARAGES`, `GARAGE_NAMES`, `GARAGE_LOCATIONS`, `FLOOR_NAMES`, `TIME_SLOTS`, `TIME_LABELS`, `CATEGORIES` must stay as constants |

---

## 15. Recommended integration pattern

Instead of gutting `mockData.ts` all at once, add a parallel API layer:

1. Create `src/app/api/client.ts` — your base `fetch`/`axios` instance with auth headers
2. Create `src/app/api/` files per domain: `bookings.ts`, `organizations.ts`, `events.ts`, `auth.ts`
3. Each API file exports async functions that return the same TypeScript types from `mockData.ts`
4. Swap out imports in each page one at a time, falling back to mock data during development
5. Use React Query or SWR for caching if your backend has latency

Example:
```typescript
// src/app/api/bookings.ts
import type { Booking } from '../data/mockData';

export async function getBookings(garageId: string, date: string): Promise<Booking[]> {
  const res = await fetch(`/api/bookings?garageId=${garageId}&date=${date}`, {
    headers: { Authorization: `Bearer ${getToken()}` },
  });
  if (!res.ok) throw new Error('Failed to fetch bookings');
  return res.json();
}
```

---

## 16. Auth flow

Current stub behavior (in `AppContext.tsx`):
- App loads already logged in as `mockUser` (member)
- A sidebar toggle lets you switch between member/fan views for demo

Real implementation should:
1. On app load, call `GET /api/auth/me` — if 200, populate `user` state; if 401, set `isLoggedIn = false` and redirect to `/login`
2. `login()` function: call `POST /api/auth/login`, store token, set user state, navigate to `/dashboard`
3. `logout()` function: call `POST /api/auth/logout`, clear token, set `user = null`, navigate to `/`
4. Protect dashboard routes: in `DashboardLayout`, if `!isLoggedIn` redirect to `/login`
5. Remove the `switchAccountType` demo toggle from the sidebar in production (or gate it behind an admin flag)

---

## 17. Email format

- Emails are **not** restricted to `@ucf.edu`
- Default placeholder in forms: `you@example.com`
- Mock data uses `@garagejam.app` domain (e.g. `alex@garagejam.app`)

---

## 18. Environment variables

Create a `.env` file at the frontend root:
```
VITE_API_URL=http://localhost:3000   # your backend base URL
VITE_APP_ENV=development
```

Use in code as `import.meta.env.VITE_API_URL`.

---

## 19. Dependencies to install in the monorepo frontend

If your monorepo frontend package.json doesn't already have these, add them:

```json
{
  "dependencies": {
    "react-router": "^7.13.0",
    "lucide-react": "^0.487.0",
    "sonner": "^2.0.3",
    "motion": "^12.0.0",
    "react-hook-form": "7.55.0",
    "clsx": "^2.1.1",
    "tailwind-merge": "^3.2.0",
    "class-variance-authority": "^0.7.1"
  },
  "devDependencies": {
    "tailwindcss": "^4.1.0",
    "@tailwindcss/vite": "^4.1.0",
    "tw-animate-css": "^1.3.0",
    "@vitejs/plugin-react": "^4.7.0"
  }
}
```

---

## 20. Quick-start checklist for Codex

- [ ] Copy `src/` from the Figma export into the monorepo frontend package
- [ ] Copy `src/styles/` (fonts, tailwind, theme, index CSS)
- [ ] Ensure `vite.config.ts` uses `@tailwindcss/vite` plugin
- [ ] Ensure `main.tsx` imports `src/styles/index.css` and renders `<App />`
- [ ] Install all dependencies listed in section 19
- [ ] Create `src/app/api/client.ts` with base fetch wrapper
- [ ] Replace `AppContext.tsx` internals with real auth (keep same interface)
- [ ] Replace mock arrays in `mockData.ts` with API calls one page at a time
- [ ] Keep all TypeScript types and business constants in `mockData.ts` unchanged
- [ ] Add route guard in `DashboardLayout` to redirect unauthenticated users to `/login`
- [ ] Test fan vs member access rules on `/garages` and sidebar nav
