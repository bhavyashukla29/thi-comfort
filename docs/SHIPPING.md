# Shipping it

Getting an app onto Google Play as a new individual developer in India involves considerably more than uploading a build. This is what the path actually looked like, because the compliance work turned out to be a larger share of the project than most of the code.

---

## Play App Signing

Google holds the app signing key; the developer holds an upload key. A practical consequence that catches people out: a locally built APK can no longer be installed over a Play-installed build, because the signatures differ by design.

The workaround used here is an `applicationIdSuffix` of `.debug` on debug builds, so a development build and the real Play build coexist on the same device instead of fighting over one package name.

---

## The 12 testers / 14 days requirement

Personal developer accounts created after 13 November 2023 cannot publish to production until they have run a closed test with **at least 12 testers, continuously opted in for at least 14 days**, and then applied for production access.

Details that matter and are easy to get wrong:

- **Closed testing and License Testing are different systems.** Being on the app's tester list does not make purchases free or simulated. That is a separate list in a separate part of the console, and discovering this the hard way means watching a real card get declined on a test purchase.
- **The clock is per tester, not global.** One person leaving on day ten doesn't reset everyone; but their replacement starts a fresh 14 days of their own, so a late dropout can cost you a fortnight.
- **Uninstalling is not opting out.** A tester who deletes the app still counts. Only explicitly leaving the programme removes them.
- **Engagement is assessed, not just enrolment.** Testers who install once and never open the app read as inactive at review time. Recruiting twelve people who each install and forget is a way to fail while technically satisfying the rule.
- **Shipping updates mid-test does not reset the clock.** Worth knowing, because it looks like it might.

The testers here were practising veterinary and farm-health professionals — the app's actual target users rather than friends installing a favour.

---

## Production access

An application form covering how testers were recruited, what engagement looked like, what feedback came back, what changed because of it, and how the app was judged ready.

The strongest material for this came from a genuine loop: a tester reported the top bar merging into the status bar, it was traced to enforced edge-to-edge display under targetSdk 36, fixed once in a shared base class, shipped, and confirmed by testers — all inside the testing window. Answering "what did you change because of feedback" is considerably easier when there is a real answer.

Approval arrived in about a day.

---

## India: RBI PA-CB and BillDesk

India-specific and recent. Under the Reserve Bank of India's Payment Aggregator – Cross Border regulations, a regulated processor handles payments between Indian developers and users outside India, and runs its own KYC separately from Google's.

The distinction that matters in practice: this governs **cross-border** sales. Domestic sales — Indian users paying an Indian developer — are outside that regime. Merchants who began selling on or after 1 January 2026 must complete verification before they can sell to users outside India.

For a launch scoped to India, this is not a blocker. It becomes one the day the app expands beyond it — which is a reason to launch India-first rather than a limitation of doing so.

---

## Store listing

Separate from the release and reviewed on its own track, which means listing fixes don't have to wait for a build.

- Screenshots are the single largest influence on whether someone installs. Stale ones showing a UI that no longer exists are worse than none.
- Tablet screenshots are required. Where a phone-shaped app has no meaningful tablet layout, the honest approach is to present real screenshots on a correctly sized canvas — not to generate synthetic UI images, which misrepresents the app and risks rejection.
- Data safety declarations, content rating, and target audience all have to be right before review rather than corrected after.

---

## Release, and what it looked like

The build promoted to production was the exact bundle validated in closed testing — promoted rather than rebuilt, so the binary that shipped was the one that had actually been tested.

Review completed in **43 minutes**.

One post-publication quirk worth recording: accounts removed from the closed testing track continued to see the app labelled "(Beta)" with no install option for some time afterwards, while every account that had never been a tester saw a completely normal listing. Google's authoritative record updated immediately — the opt-in link correctly reported the account was no longer a tester — but the Play serving layer kept a cached entitlement. Verifying from an anonymous, signed-out context was what distinguished "the listing is broken" from "these specific devices hold stale state."

---

## In-app updates

Added before public launch rather than after, for a reason worth stating: the update-prompt code has to already be present in the version on a user's phone for a prompt to ever appear. Shipping it at launch means every user has it from their first install. Shipping it later leaves the entire first cohort permanently unable to be prompted until each of them manually updates once.

Flexible mode rather than immediate: the update downloads in the background and offers a restart, instead of blocking the app. For users on poor rural connectivity, a forced download standing between them and a reading is the wrong trade.
