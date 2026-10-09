---
name: quality-loop
description: Raise and hold product quality for an iPhone app, website, web app, game, or other software service across four dimensions (UI/UX experience, function, consumer trust, engineering) with a scored bar, hard floors, an independent judge, and a ledger that ratchets every fix. Use to audit quality, run improve rounds until the bar is met, gate a release candidate, or maintain quality on each change or on a schedule. Triggers: 완성도, 품질 기준, 품질 개선 루프, production-ready, quality gate, quality audit.
license: MIT
---

# quality-loop

One standard and one loop for "is this product good enough for a paying consumer, and does it stay that way?"

- **Bar**: every in-scope dimension scores >= 4 of 5, average >= 4.3, all floors pass. A project can raise it; floors never relax.
- **Floor**: a hard minimum (crash, data loss, broken core journey, accessibility minimum, deceptive design...). Any floor breach caps its dimension at 3 and blocks release.
- **Receipt**: evidence bound to one build (commit or build number, date, device, language). A receipt from another build is stale.
- **Ledger**: the project's durable quality record. Read it first, write it last.
- **Ratchet**: every fixed defect leaves a guard (test, lint rule, CI check, checklist line) so quality only moves up.
- **Judge**: a fresh-context agent that sees only receipts, the scorecard, and the bar. A self-score never closes a gate.

Green tests prove the code does what the tests say. They do not prove the product is good. Score the product a person actually gets.

## Files

| File | Read when |
|---|---|
| [standard.md](standard.md) | Always. The four dimensions, criterion IDs, floors, profile matrix, checks. |
| [scorecard.md](scorecard.md) | Scoring, writing findings, sending the judge prompt. |
| [found-practices.md](found-practices.md) | Planning a run, briefing delegates, before reporting done. Lessons from real releases. |
| [references.md](references.md) | Citing or challenging a number. |
| [templates/quality.md](templates/quality.md) | The project has no `.quality.md` override yet. |
| [templates/ledger.md](templates/ledger.md) | The project has no ledger yet. |

## Modes

| Mode | Does | Edits product code |
|---|---|---|
| `audit` | Steps 0-4, then report. | No |
| `improve` (default) | Steps 0-8, fix rounds until the bar or the round cap. | Yes |
| `maintain` | Step 0, then steps 1-7 scoped to what changed since the last ledger receipt, plus field signals (CS-7). Full audit on a release candidate. Runs per change, per PR, or on a schedule (`/loop`). | Yes, within scope |

## Specialist skills

Use these when installed; otherwise use the fallback in `standard.md`. Name them in every delegate prompt: delegates do not inherit skills.

| Need | Skill |
|---|---|
| Aesthetic direction, visual critique, polish | `impeccable` |
| Mobile usability rules, 7-category UX rubric | `mobile-ui-ux` |
| Interaction bugs via seeded personas and invariants | `bughunt` |
| Browser checks, Lighthouse, scripted journeys | `browser-qa`, `playwright-cli`, or Chrome DevTools |
| Security review | `security-review` |
| Claims of done | `verification-before-completion` |

## The loop

### 0. Frame

Read repository instructions, the override (`.quality.md`, else `docs/quality.md`), and the ledger (path in the override, default `QUALITY-LEDGER.md`). If no override exists, draft one from `templates/quality.md`: infer the profile, core journeys, devices, and languages from the code; leave reference products and the dimension list for the owner.

Before a fix round, the owner confirms three things: the dimensions in scope, the core journeys, and 1-3 reference products the result must hold up against. Audit mode may proceed on stated defaults.

Done when: profile, dimensions, core journeys (each with a done-when), devices, languages, reference products, bar, and budgets are written in the override.

### 1. Baseline

Identify the build: commit, dirty diff, build number or deployed URL, tool versions. Build or serve the **shipped artifact** (release/exported build, production bundle), not only the editor or a debug run. Check the ledger: a criterion whose receipt is bound to this exact build or source hash is already verified; skip it and say so.

Done when: build identity is recorded and the evidence matrix (criteria x devices x languages x states) lists what this round will capture.

### 2. Collect receipts

Run the checks in `standard.md` for every in-scope criterion: journeys through the real UI from a fresh install, screenshots at the smallest and largest device (and iPad compatibility mode for iPhone apps), measurements, test runs, store/legal checks. Use separate test profiles and data; leave the owner's devices and data alone. Name screenshots `<screen>_<device>_<lang>_<state>.png` and keep them in the override's `evidence_dir`.

Done when: every in-scope criterion has a receipt or is listed as an evidence gap. A gap is reported as a gap, never as a pass.

### 3. Score and write findings

Score each dimension with `scorecard.md`. Mark each criterion pass, fail, or gap. Write each finding as: criterion ID, severity (blocker, major, minor, polish), receipt, and a concrete change with numbers.

Done when: the score sheet is complete and every finding cites a receipt.

### 4. Gate

The bar is met when every in-scope dimension passes, all floors pass, and, for a release candidate, the judge agrees. Send the judge prompt in `scorecard.md` to a separate agent with receipts only.

Bar met: go to step 8. Audit mode: report and stop. Otherwise: step 5.

### 5. Fix round

Take blockers first, then the largest score gain per effort; at most 8 findings per round. Fix the root cause. Keep every existing check at full strength: a failing check is diagnosed at its layer, and a test changes only when the spec says the new behavior is correct, with the reason in the commit. For each fix add a ratchet guard and name it in the ledger. Preserve unrelated work and existing features.

Done when: each finding in the round is fixed with a guard, or deferred with a reason.

### 6. Verify

Rebuild the shipped artifact. Re-run every check the round touched plus the full regression suite (core and UI tests together). Recapture receipts for touched screens. The judge re-scores with the previous round's findings listed and marks each fixed, partly fixed, or not fixed.

Done when: new receipts exist for this build and the judge has scored it.

### 7. Record and repeat

Append the round to the ledger: build, date, scores, fixed, open, guards added, and which future changes invalidate each receipt. Keep the round counter in the ledger, not in memory. Bar met: step 8. Below the bar and under the round cap (default 3 per session): step 5. Cap reached: stop and report the remaining findings with receipts; churning past the cap wastes the owner's money.

### 8. Close

Write the verification record to the ledger (scope, date, build, judge verdict). Stop simulators, emulators, servers, and processes this run created; delete simulator clones it made. Report in this shape:

```
quality-loop · improve · profile: ios-app · build 1.2.0 (14) @ a1b2c3d · 2026-10-09
Bar: dims >= 4, avg >= 4.3, floors pass · Judge: independent
Experience 4.5 · Function 4.0 · Consumer 4.5 · Engineering 4.0 → avg 4.25 FAIL
Floors: all pass
Fixed this run (3 rounds): [FN-2] resume after kill lost draft (guard: test_resume_draft) ...
Open, in order of impact: 1. [EN-3] first frame 620 ms on SE (launch_se3.trace) -> defer font load, target 400 ms
Evidence gaps: VoiceOver pass on settings not captured
Ledger: QUALITY-LEDGER.md round 3
```
