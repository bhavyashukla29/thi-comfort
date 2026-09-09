# Engineering notes

Problems from this project that turned out to be more interesting than they first looked. Each one is written up as it was actually diagnosed, including the false starts, because the diagnosis is usually the useful part.

---

## A purchase that appeared to do nothing

**Symptom.** Completing a subscription purchase left the UI unchanged. No crash, no error toast, no log line that obviously pointed anywhere. The purchase itself went through — Play confirmed it — but the app never visibly acknowledged it.

**Diagnosis.** Logcat showed `ViewRootImpl$CalledFromWrongThreadException` being thrown and swallowed. Google Play Billing delivers its callbacks on a background thread, and the callback was mutating views directly. Android throws when a non-UI thread touches the view hierarchy, and because the throw happened inside a library callback, nothing surfaced it to the user or to the crash reporter.

**Fix.** Marshal all UI work in billing callbacks back to the main thread explicitly.

**What made it interesting.** The failure was invisible in exactly the way that matters most — a silent no-op on the one flow that takes the user's money. The bug wasn't in logic; it was in threading assumptions that the library's API doesn't force you to think about.

---

## Localisation that was complete, correct, and unavailable

**Symptom.** Hindi and Gujarati strings were fully translated, in parity with English, and present in the built bundle. On real devices installed from Play, most users still saw English.

**Diagnosis.** Android App Bundles split resources per device by default, and that includes language resources. Play serves each device only the languages it thinks that device needs. A device set to English gets an app with only English resources — so an in-app language picker that lets a user *choose* Gujarati has nothing to switch to.

**Fix.**

```kotlin
bundle { language { enableSplit = false } }
```

**What made it interesting.** Everything testable locally was correct. The debug build was correct. The bundle contents were correct. The bug only existed in the delivery layer between Play and the device, which is invisible from the codebase — and it defeated the app's single most important feature for its actual audience.

---

## Forced edge-to-edge, and the ad nobody could fully see

**Symptom.** A tester reported the top bar merging into the phone's status bar — the menu button and the clock overlapping into an unreadable mess.

**Diagnosis.** The app targets SDK 36. From Android 15, edge-to-edge display is enforced with no opt-out: the system bars become transparent overlays and app content draws underneath them unless the app explicitly accounts for it. The codebase had no `WindowInsets` handling anywhere, so every screen was exposed, not just the reported one.

Checking the layout for the fix surfaced a second instance of the same root cause: the AdMob banner is the last child of the root layout, pinned to the bottom edge, and the navigation bar was drawing over part of it. A partially obscured ad is an ad-viewability problem, not only a cosmetic one.

**Fix.** Read the live `systemBars()` insets and apply them as padding to the content root, once, in `BaseActivity`. Because every activity already inherited from it, one change covered all six screens. Reading real values rather than hardcoding a bar height means it stays correct across notches, gesture vs. button navigation, rotation and split-screen.

**What made it interesting.** The reported symptom was one screen; the actual defect was systemic. And fixing it properly required noticing that the same cause had a second, unreported victim at the opposite edge of the screen.

---

## Onboarding that would have missed everyone who asked for it

**Symptom.** Testers said the multi-shed concept wasn't self-explanatory. The obvious fix was to explain it during onboarding.

**Diagnosis.** `OnboardingActivity` sets a `has_seen_onboarding` flag and never runs again. That flag was already `true` on every existing install — including every tester who had raised the feedback. Adding a slide there would have shown it exclusively to people who had never used the app, and never to the people who had asked.

**Fix.** The guided tour uses its own preference key, which is unset on existing installs and therefore fires for everyone on their next launch.

**What made it interesting.** The straightforward implementation would have shipped, passed review, worked exactly as coded, and delivered the feature to precisely the wrong population — while looking correct in testing on a fresh install.

---

## Subscription state after a reinstall

**Symptom.** A returning subscriber reinstalling the app has an entitlement Play knows about, but no local record of which plan or billing period they're on.

**Diagnosis.** With no backend there is no authoritative source for that detail. Play can confirm *that* someone is subscribed; reconstructing the full local state is not guaranteed.

**Fix, and its limits.** Auto Backup rules preserve what they can, and the UI handles the unknown-period case explicitly rather than rendering blanks: it marks the state as unknown, shows a reduced but honest view, and times out gracefully into a sensible default.

**What made it interesting.** This is the clearest place where "no backend" has a real cost. It's mitigated rather than solved, and pretending otherwise would be dishonest — full correctness needs server-side state the app deliberately doesn't have.

---

## Ads that stopped filling

**Symptom.** Banner ads rendered on one screen but not others, during a period of heavy testing.

**Diagnosis.** Not a code defect. AdMob rate-limits ad serving when a small number of devices generate concentrated, repetitive traffic against a single ad unit — which is exactly what testing an app looks like. The initial assumption was a bug in the ad lifecycle; Logcat showed the requests were being made and legitimately returning no fill.

**Fix.** Separate ad unit IDs per screen, plus a backoff-and-retry on failure rather than a single silent attempt.

**What made it interesting.** The tempting conclusion was "my code is broken." The evidence said the code was fine and the environment was responding rationally to abnormal traffic. Distinguishing those two saved a rewrite that would have fixed nothing.

---

## Location selection that couldn't silently be wrong

**Original approach.** Adding a shed geocoded whatever the user typed and took the first result.

**Problem.** A first result is often confidently wrong, and there is no feedback loop — the user sees a plausible reading for a place they never chose, in an app whose entire value is location-specific accuracy.

**Fix.** A debounced autocomplete where typing alone never selects anything. The user must tap a suggestion; attempting to continue without one produces an explicit error rather than a silent guess.

**What made it interesting.** The bug class here isn't a crash or a wrong pixel — it's an app that is confidently, invisibly wrong. Those are worth designing out rather than testing for.
