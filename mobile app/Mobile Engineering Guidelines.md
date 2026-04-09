# Mobile Engineering Guidelines

Framework: Flutter (Dart)
Platform: Android (development) + iOS (Expo Launch deployment)
Mirrors and extends the web app Engineering Guidelines where applicable.

---

## 1) Architecture

- Keep UI, business logic, and data concerns separated into three distinct layers:
  - **Screens** (`screens/`) — UI only, no direct API calls
  - **Providers** (`providers/`) — state and business logic, uses services
  - **Services** (`services/`) — all API calls, returns parsed models
- Screens read from providers via `context.watch` or `Consumer`. They never call services directly.
- No hardcoded strings in widget files. Labels, messages, and copy belong in a constants file or the widget's own named variable at the top of the build method.
- No business logic inside `build()` methods.

## 2) Reusability

- Any UI pattern used more than once must be extracted into a shared widget in `widgets/`.
- `EventCard` is the canonical example — used identically in Home and My Events. Do not duplicate it.
- `BookingCard`, `UcfAppBar`, `GarageFilterChips`, `LoadingSpinner`, and `ErrorBanner` are all shared widgets.
- Theme colors and text styles are always referenced from `lib/theme/colors.dart` and `lib/theme/text_styles.dart`. Never hardcode hex values or font sizes inline.

## 3) State Management

- Use `provider` package (`ChangeNotifier` + `ChangeNotifierProvider`).
- Two root-level providers:
  - `AuthProvider` — current user, JWT token, login/logout logic
  - `EventProvider` — attended events list, track/untrack logic
- Providers are injected at the root via `MultiProvider` in `main.dart`.
- Screens use `context.watch<T>()` for reactive reads and `context.read<T>()` for one-time reads inside callbacks.
- Do not use `setState` for anything that is shared across screens. `setState` is only acceptable for purely local UI state (e.g., a password visibility toggle within a single screen).

## 4) Navigation

- Use `go_router` with a `ShellRoute` for the bottom nav shell.
- Named routes only — no string literals for navigation targets spread across files. Define all route paths as constants in one place (e.g., `lib/app/routes.dart`).
- The auth guard lives in `go_router`'s `redirect` callback — not scattered across individual screens.
- Sub-screens (Event Detail) push on top of the current shell tab. They do not replace the bottom nav.

## 5) API and Data

- All HTTP calls go through `api_client.dart`. No screen or provider calls `http.get` directly.
- The base URL is always read from the `.env` file via `flutter_dotenv`. Never hardcode a URL.
- Every API response is parsed into a typed Dart model (`GarageEvent`, `User`, `Booking`). No raw `Map<String, dynamic>` objects passed around the app.
- All API calls are wrapped in try/catch. Services return a result object or throw a typed exception — never let raw HTTP errors bubble up to the UI.
- Consistent error model: services throw an `ApiException` with a `message` string and `statusCode`. Providers catch this and expose an error string that screens display.
- Auth token is always sent as `Authorization: Bearer <token>` header. The `api_client.dart` adds this automatically from `AuthProvider`'s stored token.

## 6) Git Workflow

- Mirror the web app git workflow: branch per feature/task, no direct pushes to `main`.
- Branch naming: `mobile/task-X-short-description` (e.g., `mobile/task-2-auth-flow`)
- Commit messages reference the task: `Mobile Task 2.2: implement login screen`
- Do not mix mobile and web changes in the same commit.
- Do not commit `.env` — it is in `.gitignore`.

## 7) Dart Code Style

- Use `const` constructors wherever possible — improves Flutter rendering performance.
- Prefer `final` over `var` for all variables that are not reassigned.
- Widget files contain one primary widget class. Helper widgets for that screen can live in the same file if they are small and not reused elsewhere.
- Private methods and fields use the `_` prefix.
- Avoid deeply nested widget trees. Extract sub-widgets into named private methods (`Widget _buildHeader()`) or separate `Widget` classes when nesting exceeds ~4 levels.
- Run `flutter analyze` before every commit. Zero warnings is the target.

## 8) Testing Policy

- Mirrors the web app testing policy adapted for Flutter:
  - Unit tests for all service methods and provider logic (`test/`)
  - Widget tests for shared widgets (`EventCard`, `BookingCard`, form screens) (`test/`)
  - Integration tests for critical user journeys: login flow, track event, my events load (`integration_test/`)
- Tests must pass before merging to `main`.
- Use `mockito` or `mocktail` to mock HTTP responses in unit tests — never call the live API in tests.

## 9) Performance

- Use `cached_network_image` for all remote images. Never load images directly with `Image.network`.
- Use `shimmer` loading skeletons on every screen that fetches data — never show a blank screen while loading.
- Lists use `ListView.builder` (lazy loading) — never `ListView(children: [...])` for dynamic content.
- Avoid rebuilding large widget trees on every state change. Scope `Consumer` and `context.watch` as low in the tree as possible.

## 10) Security

- JWT is stored in `flutter_secure_storage` — never in `SharedPreferences` or in memory only.
- The `.env` file is never committed. Placeholder values are documented in the roadmap.
- No user credentials (email, password) are ever logged or stored beyond the immediate auth call.
- On logout, `flutter_secure_storage` is fully cleared before routing to Login.

## 11) Theme and Visual Standards

- Black/gold/white UCF palette is enforced via `ThemeData`. No screen may deviate from the defined theme without a documented reason.
- Dark theme is used throughout — `brightness: Brightness.dark`, `scaffoldBackgroundColor: ucfBlack`.
- Gold (`#FFC904`) is used for: primary buttons, selected states, active icons, accent borders, org color fallback.
- White is used for: primary text, card titles.
- Grey (`#9E9E9E`) is used for: secondary text, unselected icons, timestamps.
- Error states use `errorRed` (`#D32F2F`).
- All interactive elements must have visible tap feedback (`InkWell` with `splashColor` or `FilledButton`).

## 12) Accessibility

- All images and icons have semantic labels (`Semantics` widget or `semanticLabel` parameter).
- Text contrast meets WCAG AA against dark backgrounds.
- Tap targets are minimum 48x48 logical pixels (Flutter's Material default).
