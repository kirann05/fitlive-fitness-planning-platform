# FitLive

Copyright (c) 2026 Sai Kiran. All rights reserved. Source is available for review; see [LICENSE](LICENSE) for terms and the treatment of earlier MIT releases.

**What is the next sensible thing I should do?**

FitLive connects sleep and readiness, strength training, meals, pantry stock and feedback into a calm daily planning experience. The interface uses light blue surfaces, restrained typography, and one primary action.

[Open the private FitLive web app](https://fitlive.kiranloginin.chatgpt.site)

## Product walkthrough

[![FitLive daily planning walkthrough](docs/media/fitlive-cover.jpg)](https://kirann05.github.io/sai-kiran-portfolio/assets/fitlive-walkthrough.mp4)

[Play the 2-minute 44-second walkthrough](https://kirann05.github.io/sai-kiran-portfolio/assets/fitlive-walkthrough.mp4) or [open the video file in this repository](docs/media/fitlive-walkthrough.mp4). The recording shows the product flow; it is not evidence that the release gates below are complete.

## What you can use now

The hosted web edition supports:

- Private sign-in and cloud-saved personal data.
- Profile, goals, dietary restrictions, allergies and editable nutrition targets.
- Manual health summaries, personal baselines, readiness check-ins and explained recovery context.
- Rotating strength sessions, set/reps/load/RPE logging, a rest timer, device-local workout drafts and cloud completion.
- Deterministic progression from completed sets and perceived effort.
- Confirmed food portions, package-label nutrition, optional USDA lookup, and source-attributed totals.
- Pantry estimates, matched food deductions, low-stock shopping lists and text export.
- Weekly training/nutrition/sleep summaries and a recommendation audit trail.
- A clearly labeled rules-based coach, without pretending an AI provider is connected.
- Custom programs/weekday schedules, recipes and meal planning, personal records and achievements.
- Optional dark appearance and explicitly consented AI explanations when configured.
- Data export and deletion.
- Explicit demo mode with sample health and nutrient fixtures.

**The complete product is still in release verification.** The web is usable; AWS deployment, live providers and native hardware checks remain explicit gates in [release status](docs/RELEASE_STATUS.md).

## Repository map

| Path | Purpose |
| --- | --- |
| `apps/web` | Hosted React/TypeScript application, cloud API, deterministic engines, migrations and tests |
| `backend` | Java 21 / Spring Boot shared account API, signed web bridge, native tokens and PostgreSQL |
| `apps/ios` | Five-section SwiftUI app, HealthKit sync, protected offline saves and combined Xcode project |
| `apps/watch` | Watch workout queue with phone review and server-save acknowledgment; hardware tests pending |
| `docs` | Architecture, decisions, privacy, API contract, validation, demo and release gaps |
| `.github/workflows` | Web checks and Java authorization tests |

Start with [architecture](docs/ARCHITECTURE.md), [decisions](docs/DECISIONS.md), [validation](docs/VALIDATION.md), and [release status](docs/RELEASE_STATUS.md).

## Hosted web development

Requires Node 22.13+.

```sh
cd apps/web
npm run install:ci
npm run dev
```

The development server prints its local URL. Its local sign-in is a test identity, not production authentication. D1 setup is described in [web setup](docs/WEB_SETUP.md).

```sh
npm test
npm run typecheck
npm run lint
npm run build
```

## Java/PostgreSQL account backend

Requires Docker Compose. OIDC is optional; the web bridge and native pairing use their own scoped authentication.

```sh
cp .env.example .env
# Fill DATABASE_PASSWORD and FITLIVE_BRIDGE_SECRET.
docker compose up --build
```

The backend is bound to localhost by default. Deploy behind HTTPS for a device. Configure the same bridge secret and `JAVA_API_URL` in hosted runtime settings to link the web. Existing D1 data is transferred only into an absent Java account. See [architecture](docs/ARCHITECTURE.md) before cutover or rollback.

```sh
cd backend
mvn test
```

## iPhone / Watch

See [native setup and verification gates](docs/NATIVE_SETUP.md). Full Xcode, signing, HealthKit entitlement and a physical device are required. iPhone and Watch source pass Apple SDK checks. The combined app still needs installed platforms, signing and real device verification.

## External services

- USDA: configure `USDA_API_KEY` as a server-side hosted secret. Food lookup fails clearly if unavailable; package-label logging still works.
- Generative AI: adapter and mocked contract tests exist. Configure server-held `OPENAI_API_KEY` and explicitly opt in before live use. Without a configured key/consent, the coach uses deterministic guidance.
- Groceries: shopping lists only. There is no purchasing endpoint and no automatic spending.
- Apple Health: native shared-account transport is implemented; deployed end-to-end and real-data verification remain unfinished.

## Limits that matter

Recovery is a conservative planning heuristic, not a validated medical score. Food values are as reliable as the selected source and confirmed portion. Unknown or incorrectly entered allergens cannot be inferred reliably; check labels. Demo data is never represented as real measurements.

The hosted Worker can delegate account storage to Java/PostgreSQL. Its standalone mode uses D1 because Sites cannot execute Java. This is recorded in [ADR-002](docs/DECISIONS.md).
