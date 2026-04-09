# Mobile App — Clarifying Questions and Answers

Recorded: 2026-04-08

---

## Q1: Flutter project location
**Q:** Convert concepts/screens from the Android skeleton into a new Flutter project, or something else? Should it live in the same repo?

**A:** Convert the concepts/screens into a new Flutter project. Keep it in the same repo under `mobile_app/`.

---

## Q2: App theme
**Q:** The Android skeleton used dark navy/purple. Should mobile use that Figma theme or the UCF black/gold/white web theme?

**A:** Use the website theme — UCF black / gold / white.

---

## Q3: Flutter + environment setup
**Q:** Have you/your team used Flutter before? How detailed should setup steps be?

**A:** Flutter SDK is downloaded but not fully configured. Android Studio Flutter plugin is NOT set up. No Mac available — development is on Windows.

**Additional clarification:** Teacher recommended Expo Launch for iOS deployment. Expo Launch is a cloud service that accepts Flutter GitHub repos, manages Apple certificates/provisioning, and builds + submits the iOS app without needing a Mac or Xcode. It is used only for the final iOS install — NOT for daily development.

Daily development and testing: Android emulator in Android Studio on Windows.
iOS install: Expo Launch (cloud build from GitHub repo).

---

## Q4: Target platform scope
**Q:** iOS only, iOS + Android, or iOS + Android + web?

**A:** Both iOS and Android. Primary delivery target is a physical iOS device via Expo Launch.

---

## Q5: "My Tickets" renamed to "My Events"
**Q:** For member accounts, what does the nav view show?

**A:** Rename to "My Events" for both account types. Both fans and members see upcoming and past events. Tab/slider at the top switches between the two (same pattern as the current Android skeleton's Upcoming/Past tabs).

---

## Q6: Fan event attendance tracking
**Q:** The backend has no endpoint for a user to mark they are attending an event. Should we add one?

**A:** Yes — make it so users can select events from the home screen to track. Those events migrate to the My Events view. Add the necessary backend endpoints to support this.

---

## Q7: Email verification on mobile
**Q:** Should mobile follow the same email verification flow as the web?

**A:** Yes, follow the same flow: register → verify email → login works.

---

## Q8: Member booking management on mobile
**Q:** Should members be able to create/cancel bookings from mobile?

**A:** Mobile is read-only. All booking management is handled on the web app.

---

## Q9: API base URL
**Q:** Live server URL and local dev URL?

**A:**
- Live: `adamdistanti.xyz`
- Local backend: port `5001` (the Express server — NOT port 5173 which is the Vite frontend)
- On Android emulator, `localhost` maps to `10.0.2.2`, so local dev URL becomes `http://10.0.2.2:5001`
- Credentials are in a local `.env` file — placeholders used in code

---

## Q10: App name
**Q:** The skeleton used "GarageBand" which is a trademarked Apple name. What should the Flutter app be called?

**A:** Garage Jam (matches the web app branding).

---

## Q11: Vault documentation
**Q (user-initiated):** Create a folder in the vault repo with markdown files split into initial setup, clarifying Q&A, and the step-by-step roadmap.

**A:** Done — this folder (`mobile app/`) contains exactly that.
