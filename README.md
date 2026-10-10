# quality-loop: an agent skill that raises product quality and keeps it there

[![Skill](https://img.shields.io/badge/Claude_Code-Skill-6b5bd6)](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview)
[![License](https://img.shields.io/badge/license-MIT-blue)](LICENSE)

Coding agents ship builds that pass every test and still feel like prototypes. Labels clip on the smallest phone, the iPad review fails, a resume bug hides half the screen, the privacy link 404s. `quality-loop` gives the agent one standard and one loop for a question tests can't answer: **is this good enough for a paying consumer, and does it stay that way?**

## What it does

- **Four dimensions** scored 1-5: **Experience** (UI/UX), **Function** (every core journey and boundary), **Consumer** (first-run success, trust, honest pricing, store accuracy), **Engineering** (tests, performance budgets, stability, security, release hygiene).
- **Ten floors** that no score can buy back: no crash or data loss in a core journey, accessibility minimums, every device class including iPad compatibility mode, declared data matches actual, no dark patterns, in-app account deletion, no weakened checks.
- **A bar**: every dimension >= 4, average >= 4.5 (quality >= 90%), all floors pass. Every report opens with the quality % and why each point was lost. For a release candidate, an independent judge has to agree. The judge sees only evidence, never the code.
- **A loop**: frame → baseline the shipped build → collect receipts → score → gate → fix round (max 8 findings) → verify → record → repeat, capped at 3 rounds per session.
- **A ledger** in the project that binds every receipt to a build, so passed checks are not re-run. A **ratchet** means every fix leaves a test or check behind, so quality only moves up.
- **Three modes**: `audit` (read-only report), `improve` (fix until the bar), `maintain` (per change, per PR, or scheduled with `/loop`).

Profiles: iPhone app, web (site, web app, PWA), game, and others mapped to the closest one. It works alongside [`impeccable`](https://github.com/pbakaus/impeccable) (visual direction), [`mobile-ui-ux`](https://github.com/cskwork/skills/tree/main/skills/design/mobile-ui-ux) (usability rubric), and [`bughunt`](https://github.com/cskwork/bughunt-skill) (interaction bugs) when they are installed, and falls back to its own criteria when they are not.

## Grounded in

ISO/IEC 25010:2023, Apple Human Interface Guidelines and App Review Guidelines, Apple's launch (400 ms first frame) and hang (250 ms) guidance, WCAG 2.2 AA, Core Web Vitals (LCP 2.5 s, INP 200 ms, CLS 0.1 at p75), OWASP MASVS v2.1 and ASVS 5.0, Google Play vitals, Nielsen's heuristics, the HEART framework, SUS, the FTC dark-patterns report, and Korea's e-commerce dark-pattern rules (in force 2025-02-14). Links are in [`references.md`](references.md).

It also carries [found practices](found-practices.md): 16 lessons from real 2026 releases with coding agents. One game passed 339 tests and still felt unfinished. One app was rejected because a dialog clipped on iPad. One resume bug came back because UI tests were missing from the runner. Each lesson became a rule.

Before publishing, the skill was forward-tested: a fresh agent with no owner and no source audited a deployed Korean web app using only these files. It found three floor breaches with receipts and reported 32 ambiguities in the wording, which this version fixes.

## Install

```sh
npx skills@latest add cskwork/quality-loop-skill
# or
git clone https://github.com/cskwork/quality-loop-skill ~/.claude/skills/quality-loop
```

Then ask your agent: "run quality-loop audit on this app", "bring this site to the quality bar", or "quality-loop maintain before the release candidate".

## Files

| Path | What |
|---|---|
| `SKILL.md` | Modes and the loop, step by step, each step with its completion criterion |
| `standard.md` | Dimensions, floors, profiles, criteria with checks |
| `scorecard.md` | Anchors, severity, score sheet, independent-judge prompt |
| `found-practices.md` | Lessons from real releases and the rules they produced |
| `references.md` | Sources for every number |
| `templates/quality.md` | Per-project override (`.quality.md`): journeys, references, devices, bar, budgets |
| `templates/ledger.md` | The project's quality ledger |

## 한국어 요약

`quality-loop`은 아이폰 앱, 웹사이트, 웹앱, 게임 같은 소프트웨어 서비스의 완성도를 높이고 유지하는 에이전트 스킬입니다. 네 가지 기준으로 1~5점을 매깁니다. **경험**(UI/UX), **기능**(핵심 사용 흐름과 경계 상황), **소비자**(첫 사용 성공, 신뢰, 정직한 가격, 스토어 정확성), **개발 완성도**(테스트, 성능 예산, 안정성, 보안, 출시 위생)입니다.

통과 기준은 모든 항목 4점 이상, 평균 4.5점 이상(품질 90% 이상), 최소 기준(floor) 10개 전부 통과입니다. 출시 후보는 코드를 보지 않고 증거만 보는 별도 심사 에이전트가 판정합니다. 루프는 기준 정하기 → 출시 빌드 확인 → 증거 수집 → 채점 → 판정 → 수정(한 번에 최대 8건) → 재검증 → 기록 순서로 돌고, 세션당 3회가 넘으면 멈추고 보고합니다. 모든 검증 결과는 프로젝트 원장(ledger)에 빌드 단위로 기록해서 같은 검사를 반복하지 않습니다. 고친 결함마다 테스트나 체크 항목을 하나씩 남겨서 품질이 다시 떨어지지 않게 막습니다.

## License

MIT
