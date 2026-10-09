# The standard

Four dimensions, each scored 1-5 (`scorecard.md`). Criterion IDs (`FN-2`, `EN-3`) go in findings, commits, guards, and ledger entries. Sources for every number: `references.md`.

| Dimension | Question | ISO/IEC 25010:2023 characteristics it covers |
|---|---|---|
| **Experience** (UX) | Can a person read, reach, understand, and enjoy it on every device it runs on? | Interaction capability |
| **Function** (FN) | Does every core journey work end to end, through every boundary, without losing data? | Functional suitability, reliability, compatibility |
| **Consumer** (CS) | Would a first-time paying consumer succeed, trust it, and choose it over the reference product? | Interaction capability, security (privacy), safety |
| **Engineering** (EN) | Is it built so quality holds: fast, stable, secure, tested, releasable? | Performance efficiency, reliability, security, maintainability, flexibility |

## Floors

Any breach caps its dimension at 3 and blocks a release. An override cannot relax a floor.

| ID | Floor | Dim |
|---|---|---|
| F-1 | No crash, freeze, or soft-lock in any core journey on the shipped artifact. A freeze is a main-thread stall a person notices; Apple's tools flag main run loop busy > 250 ms. | FN |
| F-2 | No user data lost or corrupted across kill, relaunch, background, update, or a failed write. | FN |
| F-3 | Every core journey completes from a fresh install through the real UI. | FN |
| F-4 | No placeholder text, lorem ipsum, TODO, example.com, dead links, debug UI, or dev endpoints in the shipped artifact. | CS |
| F-5 | Accessibility minimums: text contrast >= 4.5:1 (3:1 large); targets >= 44x44 pt on iOS, >= 24x24 CSS px on web (WCAG 2.5.8 AA); iOS text >= 11 pt; web reflows at 320 CSS px; drag actions have a tap/click alternative. | UX |
| F-6 | Every screen fits and operates on every device class the product runs on, including iPad compatibility mode for iPhone-only apps (App Review tests there). | UX |
| F-7 | What the product collects and sends matches what the privacy policy, store privacy answers, and privacy manifest declare. No secret keys in the client or the repo. | CS / EN |
| F-8 | No deceptive design: total price shown before checkout, no hidden renewal, no pre-selected paid options, no misleading visual hierarchy, cancel or delete is as easy as sign-up, no repeated nagging after a refusal. | CS |
| F-9 | If accounts exist, account deletion starts inside the product. | CS |
| F-10 | Checks keep full strength. A disabled, skipped, or loosened check counts as failing. | EN |

## Profiles

Pick from the override or infer. Hybrids (a game shipped on web) use both rows.

| Profile | Platform guidance | Smallest / largest evidence | Extra criteria |
|---|---|---|---|
| `ios-app` | Apple HIG, App Review Guidelines | iPhone SE (375x667 pt) / Pro Max (440x956 pt) + iPad compat mode or native iPad | Store rows in CS-5 |
| `web` | WCAG 2.2 AA, Core Web Vitals | 320 / 375 / 440 CSS px mobile + a 1280 px desktop | EN-3 web budgets |
| `game` | Apple HIG Games + the genre's reference games | Same as its platform | GM-1..GM-4 |
| other (Android, desktop, CLI) | Closest profile, with that platform's own guidelines and store vitals | Platform's smallest and largest | Same IDs |

## Experience (UX)

| ID | Criterion | Check |
|---|---|---|
| UX-1 | Usability rules and rubric pass. | `mobile-ui-ux` rubric for phone UIs (all 7 categories >= 4, avg >= 4.3). Desktop web or fallback: severity-rated review against NN/g's 10 heuristics plus WCAG 2.2 AA. |
| UX-2 | Deliberate visual direction and craft; nothing reads as a template default. | `impeccable` critique against the brand notes in the override. |
| UX-3 | Every primary screen has designed empty, loading, error, offline, limit-reached, and success states. | Capture each state; list the ones that cannot be reached. |
| UX-4 | Copy is plain, consistent, in the user's language; every error says what happened and what to do next. | Read every string on the evidence screens; NN/g heuristic 9. |
| UX-5 | Holds up side by side with the reference products on the same journey. | Same-journey screenshots next to each reference; list gaps. |
| UX-6 | Works across the settings matrix: light/dark, largest supported text size, Bold Text, Reduce Motion, VoiceOver/screen reader, every shipped language. | One receipt per setting on the screen with the most text. |

## Function (FN)

| ID | Criterion | Check |
|---|---|---|
| FN-1 | Every core journey in the override reaches its done-when on the shipped artifact. | Run each journey through the real UI from a fresh install; record steps and result. |
| FN-2 | Boundaries hold: save → kill → cold launch → resume at every persisted phase; background/foreground; interruptions mid-transition; offline and slow network; permission denied. | `bughunt` save-resume, interruptions, slow-player personas; else walk each by hand. |
| FN-3 | Inputs hold: empty, maximum length, emoji, Korean/CJK IME composition, paste, rapid double submit (idempotent), invalid values rejected with a reason. | Scripted or manual input set per form. |
| FN-4 | Writes are atomic; a damaged save blocks overwrite until the user picks a recovery; data from the previous released version migrates. | Kill during write; load a previous-version fixture. |
| FN-5 | No dead ends: every screen can be left; every failure shows a reason and a way forward. | Walk back/close from every screen in the evidence matrix. |
| FN-6 | Every feature claimed in onboarding, store listing, or website exists and works. | Diff the claims against FN-1 receipts. |

## Consumer (CS)

| ID | Criterion | Check |
|---|---|---|
| CS-1 | A first-time user reaches first value without help, no slower than in the reference product. | Time and count taps from fresh install to first value; same for the reference. |
| CS-2 | New users complete the core journeys. | 5 new users per round (qualitative; finds most problems per NN/g). If no humans are available, 5 fresh-context agent personas, labeled **simulated**, never reported as user testing. Optional SUS for humans: 68 is average, 80 a common industry target. |
| CS-3 | Trust surfaces: privacy policy and terms reachable in the product and on a public URL, a support contact, a feedback path, honest pricing. | Open each link from the product; check dates against the latest data/SDK change. |
| CS-4 | Permissions are asked in context, with a specific purpose string, and the product stays usable if denied. | Deny each permission and rerun the affected journey. |
| CS-5 | Store and listing accuracy: screenshots are real screens of this build, text matches behavior, metadata within limits; the platform's review guidelines pass (App Review 2.1 completeness, 2.3 accuracy, 4.2 minimum functionality, 5.1 privacy). | Compare listing with FN-1 receipts; walk the project's release checklist if it has one. |
| CS-6 | Feels fast: visible feedback within 100 ms of a tap; launch to usable within EN-3 budgets. | Screen recording at 60 fps on the smallest device; count frames from tap to response. |
| CS-7 | Field signals are watched (maintain mode): store reviews, ratings, support mail, crash and hang reports, HEART goal → signal → metric for each core journey. | Each new signal becomes a finding or a ledger note. |

## Engineering (EN)

| ID | Criterion | Check |
|---|---|---|
| EN-1 | Core logic and every core journey have automated tests, and the default test command runs all suites (unit, UI, integration). Tests run before every push (pre-push hook or CI). | Run the default command; confirm UI suites are in it. |
| EN-2 | Verification uses the shipped artifact; QA instrumentation is off in normal launches. | Receipt names the artifact hash or build number. |
| EN-3 | Performance budgets hold on the smallest supported device. iOS: first frame <= 400 ms (Apple's target), no main run loop busy > 250 ms in core journeys, animations without visible hitches. Web, p75: LCP <= 2.5 s, INP <= 200 ms, CLS <= 0.1 (field data when available, else Lighthouse mobile as a lab proxy, labeled lab). | Instruments App Launch / Hangs, Xcode Organizer, `XCTApplicationLaunchMetric`; CrUX or Lighthouse. |
| EN-4 | Field stability: crash and hang reporting is on (with consent where required); crash rate stays under the store's bad-behavior line (Google Play: user-perceived crash 1.09 %, ANR 0.47 %); on iOS no release-over-release rise in Organizer crashes, hangs, or terminations. | Store dashboards per release. |
| EN-5 | Security and privacy engineering: no secrets in client or repo; sensitive data stored and sent safely; server enforces authorization. Mobile: OWASP MASVS v2.1 categories (STORAGE, CRYPTO, AUTH, NETWORK, PLATFORM, CODE, RESILIENCE, PRIVACY). Web and services: OWASP ASVS 5.0. | `security-review`; secret scan of repo and built bundle. |
| EN-6 | Release hygiene: reproducible build from clean committed source; version and build number bumped; debug flags and dev endpoints off; asset and dependency licenses recorded. | Build from a clean checkout; grep the artifact. |
| EN-7 | Observability: errors are reported with context; analytics cover core-journey task success, within consent. | Trigger a handled error; find it in the report. |
| EN-8 | Services with a backend: an availability and latency objective per user-facing endpoint, with timeouts, retries with backoff, and a tested failure mode in the client. | Kill or slow the backend; the client shows FN-5 behavior. |

## Games (GM)

Added to the `game` profile. Set at kickoff with the owner, before building.

| ID | Criterion | Check |
|---|---|---|
| GM-1 | Reference games (1-3) and presentation level (3D, 2.5D, 2D) are named in the PRD, and the build matches that level. | Read the PRD; compare a frame strip. |
| GM-2 | The core loop is visible and felt: animated idle, move, act, hit, defeat; distinct effects per action type; real recorded, licensed audio (placeholder beeps never ship); pacing readable at 1x. | Recording or 3-5 frame strip of the busiest moment; audio file list with licenses. |
| GM-3 | A new player's first ~10 minutes through the real UI teach themselves: no manual, no instant loss, no soft-lock. | New-player playtest on the target device or simulator, recorded. |
| GM-4 | Gaps against the reference games are listed, not hidden. | Written gap list in the ledger. |
