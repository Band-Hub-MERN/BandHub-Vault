# Garage Jam Mobile App — Step-by-Step Roadmap

Last updated: 2026-04-08

Framework: Flutter (Dart)
Targets: Android (daily dev/testing) + iOS (final install via Expo Launch)
Development machine: Windows
iOS deployment: Expo Launch — cloud build, no Mac or Xcode required
Theme: UCF black / gold (#FFC904) / white
Location in repo: `mobile_app/garage_jam/`

---

## Overview of Phases

| Phase | Title | Deliverable |
|-------|-------|-------------|
| 0 | Environment Setup | Flutter + Android Studio + Android emulator all green on Windows |
| 1 | Flutter Project + Theme | New project, folder structure, UCF colors, bottom nav shell |
| 2 | Auth Flow | Login, Register, Email Verification screens + JWT storage |
| 3 | Home — Events Feed | Live events from API, event cards, garage filter, track/untrack |
| 4 | My Events | Upcoming/Past tabs, tracked events, member read-only bookings |
| 5 | Settings | Profile display, edit display name, notification prefs, sign out |
| 6 | Backend Addition | New attendance endpoints on Express server |
| 7 | Polish + Android Testing | Loading states, error handling, emulator QA |
| 8 | iOS Deploy via Expo Launch | Connect GitHub repo, cloud build, install on iPhone |

---

## Phase 0 — Environment Setup (Windows)

### Step 0.1 — Verify Flutter SDK is in PATH
Open a terminal and run:
```bash
flutter --version
```
If this errors, add the Flutter SDK `bin` folder to your Windows PATH:
- System Properties → Advanced → Environment Variables → Path → Add the path to your `flutter/bin` folder

### Step 0.2 — Install Android Studio Flutter + Dart Plugins
1. Open Android Studio.
2. Settings → Plugins → Marketplace.
3. Search "Flutter" → Install (Dart installs automatically with it).
4. Restart Android Studio.
5. Verify: File → New → New Flutter Project should now be an option.

### Step 0.3 — Accept Android Licenses
In a terminal:
```bash
flutter doctor --android-licenses
```
Accept every prompt with `y`.

### Step 0.4 — Create an Android Virtual Device (Emulator)
1. In Android Studio: Tools → Device Manager → Create Device.
2. Choose a Pixel device (Pixel 7 recommended).
3. Select a system image — download API Level 34 (Android 14) if not already present.
4. Finish and launch the emulator to confirm it boots.

### Step 0.5 — Run Flutter Doctor
```bash
flutter doctor
```
Expected green checks:
- Flutter SDK ✓
- Android toolchain ✓
- Android Studio ✓
- Connected device (your running emulator) ✓

The Xcode line will show a warning — **ignore it**. You are on Windows and Xcode is not needed for development. iOS is handled by Expo Launch at the end.

---

## Phase 1 — Flutter Project + Theme Shell

### Step 1.1 — Create the Flutter Project
From the repo root in a terminal:
```bash
cd mobile_app
flutter create garage_jam --org com.garageband --platforms android,ios
cd garage_jam
```
The `ios` platform is included so that Expo Launch can build it — you will not build iOS locally.

### Step 1.2 — Clean Default Files
Delete the counter demo content from `lib/main.dart`. The file should just call `runApp(GarageJamApp())` pointing to a placeholder widget for now.

### Step 1.3 — Set Up Folder Structure
```
lib/
  main.dart
  app.dart                   # MaterialApp + theme + router
  theme/
    colors.dart              # UCF palette constants
    text_styles.dart         # typography
    theme.dart               # ThemeData
  services/
    api_client.dart          # base HTTP client, reads base URL from .env
    auth_service.dart        # login, register, me, verify, register-status
    event_service.dart       # events list, detail, attend, unattend, attending
    booking_service.dart     # read-only bookings/mine
  models/
    user.dart
    garage_event.dart
    booking.dart
  providers/
    auth_provider.dart       # current user + JWT token (ChangeNotifier)
    event_provider.dart      # attended events list (ChangeNotifier)
  screens/
    auth/
      splash_screen.dart
      login_screen.dart
      register_screen.dart
      email_verification_screen.dart
    home/
      home_screen.dart
      event_detail_screen.dart
    my_events/
      my_events_screen.dart
    settings/
      settings_screen.dart
  widgets/
    event_card.dart          # shared event card used in Home and My Events
    booking_card.dart        # read-only booking card for member My Events
    garage_filter_chips.dart # horizontal garage selector
    ucf_app_bar.dart         # shared top bar
    loading_spinner.dart
    error_banner.dart
```

### Step 1.4 — Add Dependencies to pubspec.yaml
```yaml
dependencies:
  flutter:
    sdk: flutter
  http: ^1.2.0
  flutter_secure_storage: ^9.0.0
  provider: ^6.1.2
  flutter_dotenv: ^5.1.0
  go_router: ^14.0.0
  intl: ^0.19.0
  cached_network_image: ^3.3.1
  shimmer: ^3.0.0
```
Run:
```bash
flutter pub get
```

### Step 1.5 — Create the .env File
Create `mobile_app/garage_jam/.env`:
```
# Live server
API_BASE_URL=https://adamdistanti.xyz

# Local dev — swap to this when testing against local backend
# API_BASE_URL=http://10.0.2.2:5001
# Note: 10.0.2.2 is how Android emulator reaches the Windows host machine's localhost
# The backend runs on port 5001 (Express), NOT 5173 (that is the Vite frontend)
```

Add `.env` to `mobile_app/garage_jam/.gitignore`:
```
.env
```

Load it at app start in `main.dart`:
```dart
await dotenv.load(fileName: '.env');
```

Also add `.env` as an asset in `pubspec.yaml`:
```yaml
flutter:
  assets:
    - .env
```

### Step 1.6 — Define UCF Theme
`lib/theme/colors.dart`:
```dart
import 'package:flutter/material.dart';

const Color ucfBlack      = Color(0xFF000000);
const Color ucfGold       = Color(0xFFFFC904);
const Color ucfWhite      = Color(0xFFFFFFFF);
const Color surfaceDark   = Color(0xFF1A1A1A);   // card backgrounds
const Color surfaceCard   = Color(0xFF111111);   // slightly lighter than pure black
const Color textSecondary = Color(0xFF9E9E9E);
const Color errorRed      = Color(0xFFD32F2F);
const Color dividerColor  = Color(0xFF2A2A2A);
```

`ThemeData` in `lib/theme/theme.dart`:
- `brightness: Brightness.dark`
- `scaffoldBackgroundColor: ucfBlack`
- `primaryColor: ucfGold`
- `colorScheme` with `primary: ucfGold`, `onPrimary: ucfBlack`, `surface: surfaceDark`
- AppBar theme: black background, gold title text
- Bottom nav: black background, gold selected icon, grey unselected

### Step 1.7 — Wire Bottom Navigation Shell
Three tabs, persistent across screens:
- **Home** — house icon
- **My Events** — calendar icon
- **Settings** — gear icon

Use `go_router` with a `ShellRoute` so the bottom nav bar stays visible when navigating to sub-screens (e.g., Event Detail). The Event Detail screen pushes on top of the current tab without replacing the nav bar.

Auth guard: if no valid token in secure storage, redirect to Login regardless of which route is accessed.

---

## Phase 2 — Auth Flow

### Step 2.1 — Splash Screen
- Black background, Garage Jam wordmark centered, gold accent line
- On load: check `flutter_secure_storage` for a saved JWT
  - Token found → call `GET /api/auth/me` to validate
    - Success → populate `AuthProvider`, route to Home tab
    - Failure (401/expired) → clear token, route to Login
  - No token → route to Login
- Show splash for at minimum 1 second so it doesn't flash

### Step 2.2 — Login Screen
Fields: Email, Password (obscured, toggle visibility icon)
Primary button: "Log In" → `POST /api/auth/login`

Response handling:
- 200 → store JWT via `flutter_secure_storage`, set user in `AuthProvider`, route to Home
- 403 → "Please verify your email before logging in. Check your inbox."
- 401 → "Invalid email or password."
- Network error → "Could not connect. Check your connection and try again."

Secondary: "Don't have an account? Register" → route to Register screen.

### Step 2.3 — Register Screen
**Step 1** — Account type selection:
- Two full-width tappable cards: "Fan" and "Member"
- Fan card: brief description ("Browse and track campus events")
- Member card: brief description ("Create an organization and manage practice sessions")
- Gold border on selected card

**Step 2** — Fields (shown after type selected):
- Email
- Display Name
- Password with live policy indicator (four checkmarks that go gold as requirements are met: 8+ chars, uppercase, number, special symbol)
- Confirm Password
- Member only: Role / Instrument label (optional, free-text)

Button: "Create Account" → `POST /api/auth/register`

On 201 success: save `registrationStatusToken` from response, navigate to Email Verification screen. Do NOT log in yet.

On 409: "An account with this email already exists."
On 400: show the specific validation error from the API.

### Step 2.4 — Email Verification Screen
Content: "We sent a verification link to [email]. Open it to activate your account."

Background polling every 4 seconds: `GET /api/auth/register-status?token=<registrationStatusToken>`

When `shouldStopPolling: true`:
- `status: "verified"` → stop polling, show success message, navigate to Login after 2 seconds
- `status: "bounced_invalid"` or similar error → show the `message` from the API response ("That email address appears not to exist…") with a "Go Back" button to re-register

Also show: "Already verified on another device? Go to Login" tap target.

Show an animated email icon or progress indicator while polling.

### Step 2.5 — AuthProvider
`ChangeNotifier` class holding:
- `User? currentUser`
- `bool isLoggedIn`
- `String? token` (also stored in secure storage)

Methods:
- `login(email, password)` → calls API, stores token, notifies listeners
- `logout()` → clears secure storage, resets state, router redirects to Login
- `refreshUser()` → calls `/api/auth/me`, updates `currentUser`
- `updateUser(User updated)` → used after PATCH /api/auth/me succeeds

Wrap the root widget in `MultiProvider` with both `AuthProvider` and `EventProvider`.

---

## Phase 3 — Home Screen: Events Feed

### Step 3.1 — Fetch Events
On screen mount: `GET /api/events` (no auth required for public events list).

Parse into `List<GarageEvent>`. Sort by date ascending.

Show shimmer placeholders (3-4 ghost cards) while loading.
Show `ErrorBanner` with retry button on failure.

### Step 3.2 — Garage Filter Chips
Horizontal scrollable row at the top:
```
All   A   B   C   D   H   I
```
- Unselected: black pill, gold text/border
- Selected: gold pill, black text

Selecting a chip filters the list client-side. "All" resets. Only one chip active at a time.

### Step 3.3 — Event Cards (shared widget)
`event_card.dart` used both in Home and My Events:
- Left border strip colored with `orgColor` from the event
- Cover image (top, with `cached_network_image` + dark placeholder on error)
- Title (white, bold)
- Org name (gold)
- Garage + floor formatted as "Garage A · Rooftop (Open Air)" using the floor name map
- Date and time formatted as "Sat, Apr 12 · 7:00 PM – 10:00 PM"
- Category badge (outlined pill, gold text)
- Attendee count ("134 attending")
- Track/Untrack icon button (bookmark outline → filled gold when tracked)

### Step 3.4 — Event Detail Screen
Full screen, pushed on top of the current tab (does not replace bottom nav).

Layout:
- Full-width cover image header with back arrow overlaid
- Org color accent bar
- Title, org name, category badge
- Date/time/garage/floor row with icons
- Description text (scrollable if long)
- Attendee count
- "Track Event" / "Untrack Event" button (full width, gold when untracked, outlined when tracked)

### Step 3.5 — Track / Untrack Logic
Track button tapped:
1. Optimistically update UI immediately (fill the bookmark icon)
2. Call `POST /api/events/:id/attend` (auth required)
3. On success: add event to `EventProvider.attendedEvents`
4. On failure: revert UI, show snackbar error

Untrack button tapped:
1. Optimistically remove from UI
2. Call `DELETE /api/events/:id/attend`
3. On success: remove from `EventProvider.attendedEvents`
4. On failure: revert UI, show snackbar error

If user is not logged in and taps Track: redirect to Login screen, preserve intent to return after login.

---

## Phase 4 — My Events Screen

### Step 4.1 — Screen Structure
Tab bar at top: **Upcoming** | **Past**
- Selected tab: gold underline, white text
- Unselected: grey text

### Step 4.2 — Data on Load
Call `GET /api/events/attending` (auth required) → split result by date vs today.

If `member` with `organizationId`: also call `GET /api/bookings/mine` → append booking cards to the appropriate tab.

Both calls run in parallel (`Future.wait`).

### Step 4.3 — Upcoming Tab
Events where `date >= today`, sorted ascending.
Member accounts also show upcoming bookings below events (visually separated by a section header "Practice Bookings").

Empty state: "No upcoming events tracked yet. Browse events on the home screen to add them here."

### Step 4.4 — Past Tab
Events where `date < today`, sorted descending (most recent first).
Member accounts also see past bookings.

Empty state: "No past events yet."

### Step 4.5 — Booking Cards (member only, read-only)
`booking_card.dart` — visually distinct from event cards:
- Dark surface background with a subtle gold left border
- "Practice Booking" label in grey (makes clear it is not an event)
- Garage + floor
- Date and time range
- "Weekly" badge if `isWeekly: true`
- Organization name
- No track/untrack button — read-only

---

## Phase 5 — Settings Screen

### Step 5.1 — Profile Header
- Circular avatar with initials (first letters of display name, gold background, black text)
- Display name (white, large)
- Email (grey, smaller)
- Account type badge: gold outlined pill showing "Fan" or "Member"

### Step 5.2 — Edit Display Name
Tapping the display name (or an edit icon) opens a bottom sheet with a text field pre-filled with the current name.
- "Save" → `PATCH /api/auth/me` with `{ displayName: newValue }`
- On success: update `AuthProvider.currentUser`, close sheet
- Email is read-only — not editable on mobile

### Step 5.3 — Notification Preferences
Four toggle rows (Switch widgets), labeled:
- Invites
- Events
- Bookings  
- Digest

Initialized from `currentUser.notificationPrefs`. Each toggle change immediately calls `PATCH /api/auth/me` with the full updated `notificationPrefs` object.

### Step 5.4 — Member-Only Section
If `accountType === 'member'`, show a read-only row:
- Label: "Organization"
- Value: org name from `/api/auth/me` response, or "No organization linked"
- No tap action — org management is web-only

### Step 5.5 — Sign Out
Bottom of the screen. Row label in `errorRed`.
Tapping shows a confirmation `AlertDialog`:
- "Sign Out" (red confirm button) → `AuthProvider.logout()` → clears secure storage → router redirects to Login
- "Cancel" dismisses

---

## Phase 6 — Backend Addition: Event Attendance

New work in `BandHub/api.js` and `BandHub/models/garageEvent.js`.

### Step 6.1 — Update GarageEvent Model
Add `attendeeIds` array to `models/garageEvent.js`:
```js
attendeeIds: {
  type: [{ type: Schema.Types.ObjectId, ref: 'AccountUsers' }],
  default: []
},
```
Keep the existing `attendees` number field as a convenience count. Keep it in sync with `attendeeIds.length`.

### Step 6.2 — POST /api/events/:id/attend
Auth required (any account type).

Logic:
1. Find event by ID → 404 if not found
2. If `user._id` already in `attendeeIds` → return 200, no change (idempotent)
3. `$addToSet` user ID into `attendeeIds`, increment `attendees` by 1
4. Return updated event in standard shape

### Step 6.3 — DELETE /api/events/:id/attend
Auth required.

Logic:
1. Find event by ID → 404 if not found
2. If user ID not in `attendeeIds` → return 200, no change (idempotent)
3. `$pull` user ID from `attendeeIds`, decrement `attendees` by 1 (floor at 0)
4. Return updated event

### Step 6.4 — GET /api/events/attending
Auth required.

Logic:
1. `GarageEvent.find({ attendeeIds: userId })`
2. Sort by `date` ascending
3. Return in same shape as `GET /api/events`

**Critical:** Register this route in `api.js` BEFORE `GET /api/events/:id`, otherwise Express matches the string `"attending"` as the `:id` parameter and returns a 404.

---

## Phase 7 — Polish + Android Emulator Testing

### Step 7.1 — Loading States
Every screen that fetches data needs:
- Shimmer skeleton or centered spinner while loading
- `ErrorBanner` widget with message + "Retry" button on network failure
- Empty state card with descriptive message when list is empty

### Step 7.2 — Pull to Refresh
Wrap the scroll views on Home and My Events in `RefreshIndicator`.
Pulling down re-calls the API and replaces the list. Gold refresh indicator color.

### Step 7.3 — Snackbar Feedback
All actions (track, untrack, save display name, toggle notification pref) show a brief snackbar:
- Success: black background, gold text
- Failure: error message with dismiss

### Step 7.4 — Emulator QA Checklist
Before moving to iOS deployment, verify on Android emulator:
- [ ] Splash → Login routing works
- [ ] Registration flow end-to-end (including email verification polling)
- [ ] Login with verified account works
- [ ] Home events load from live API
- [ ] Garage filter chips work
- [ ] Track/Untrack persists across app restarts
- [ ] My Events shows correct upcoming/past split
- [ ] Member accounts see booking cards in My Events
- [ ] Settings loads user data
- [ ] Edit display name saves and reflects immediately
- [ ] Notification toggles persist
- [ ] Sign out clears session and returns to Login

---

## Phase 8 — iOS Deployment via Expo Launch

Once Android emulator QA passes:

### Step 8.1 — Push Final Code to GitHub
Ensure the `mobile_app/garage_jam/` Flutter project is committed and pushed to the `BandHub` repo on the `main` branch (or a release branch).

### Step 8.2 — Sign Up / Log In to Expo Launch
Go to [expo.dev/launch](https://expo.dev/launch) and sign in with a GitHub account.

### Step 8.3 — Connect the Repository
- Select the `Band-Hub-MERN/BandHub` repository
- Point Expo Launch at the `mobile_app/garage_jam/` subdirectory as the Flutter project root

### Step 8.4 — Configure iOS Build Settings
Expo Launch will ask for:
- App name: **Garage Jam**
- Bundle ID: `com.garageband.garagejam`
- Apple ID credentials (for signing the app)
- Build type: Development (for device testing) or Distribution (for TestFlight)

### Step 8.5 — Trigger the Build
Expo Launch builds on their cloud Mac infrastructure. Build time is typically a few minutes.

### Step 8.6 — Install on iPhone
Expo Launch provides a download link or QR code to install the `.ipa` on your physical iPhone.
- For development builds: trust the developer certificate on the iPhone (Settings → General → VPN & Device Management → Trust)

---

## Open Items / Future Phases

1. **Invite acceptance on mobile** — endpoints exist (`GET /api/invites`, `POST /api/invites/:id/accept`). Could be a Settings sub-screen in a later phase.
2. **Push notifications** — not in scope. Would require APNs (iOS) and FCM (Android).
3. **Event creation on mobile** — web-only for now. Could be added for member accounts later.
4. **App Store / TestFlight distribution** — Expo Launch handles submission. Future milestone after MVP testing.
5. **Offline caching** — not planned. App requires network connection.
