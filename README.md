# CertFly

> Duolingo for technical certifications — short daily sessions with spaced repetition and per-topic mastery, for anyone studying data certifications across the 3 major clouds (Google Cloud, AWS, Azure).

![status](https://img.shields.io/badge/status-in%20development-yellow)
![type](https://img.shields.io/badge/type-study%20side%20project-blue)
![backend](https://img.shields.io/badge/backend-FastAPI-009688)
![frontend](https://img.shields.io/badge/frontend-Flutter-02569B)
![db](https://img.shields.io/badge/db-Supabase%20%2F%20Postgres-3ECF8E)

Solo side project, not a production product and not on a path to becoming a SaaS — it's how I found a way to study data certifications with more habit structure. The backend (rules engine + API) is functional and tested end to end against local SQLite; still missing: real content (questions), connecting a real Supabase project, and the Flutter frontend.

**Summary:** [How it looks](#how-it-looks) · [Current status](#current-status-2026-08-09) · [Stack](#stack) · [Background](#background) · [Documentation](#documentation) · [Running locally](#running-the-backend-locally) · [Roadmap](#immediate-roadmap)

---

## How it looks

<table>
<tr>
<td width="50%" align="center">
<img src="docs/screenshots/track-screen.png" alt="CertFly topic track, with streak, XP, and domains locked until mastery unlocks them" width="100%" />
</td>
<td width="50%" align="center">
<img src="docs/screenshots/profile-screen.png" alt="CertFly profile screen, with total XP, streak, active certification, and study preferences" width="100%" />
</td>
</tr>
<tr>
<td valign="top">

**Certification track.** Each certification becomes a vertical track of domains and topics, in the spirit of Duolingo for languages. At the top, the day's streak and accumulated XP — the habit engine that drives the product's core proposition. Below, topics in sequence: the next one already unlocked with a "Start" button, the following ones locked (🔒) until the mastery engine confirms 80% domain mastery on the previous topic — an unlock gate enforced server-side, not just decorative on screen.

</td>
<td valign="top">

**Profile.** Total XP and streak consolidated, the active certification (with the option to switch between the 3 clouds), and study preferences — where reminders and spaced-repetition cadence live. It's the hub that closes the habit loop started on the track.

</td>
</tr>
</table>

*Screenshots from a local build (Flutter Web + local FastAPI, SQLite, no real Supabase) — see [Running the backend locally](#running-the-backend-locally) to reproduce.*

---

## Current status (2026-08-09)

| Stage | Status |
|---|---|
| Idea validated (real potential, defensible niche) | ✅ |
| Market and competitor research | ✅ |
| MVP spec locked | ✅ |
| Architectural premise locked (provider-agnostic) | ✅ |
| Functional and non-functional requirements (FR/NFR) | ✅ |
| System design (Flutter + FastAPI + Supabase, schema, endpoints) | ✅ |
| Domain modeling (entities) | ✅ |
| Core loop engine (SRS + mastery + gate + XP/streak) | ✅ |
| Repository (SQLAlchemy) + services + FastAPI API (5 endpoints) | ✅ |
| Real content — GCP Professional Data Engineer | ⏳ next step |
| Real content — AWS Data Engineer – Associate | ⏳ not started |
| Real content — Azure Data Engineer Associate (DP-203) | ⏳ not started |
| Real Supabase connected (currently runs on local SQLite) | ⏳ next step |
| Flutter frontend | ✅ core loop screens implemented (dev target Linux); visual prototype of all 10 screens complete |

---

## Stack

| Layer | Technology | Role |
|---|---|---|
| Frontend | Flutter (mobile-first, dev target web/Linux) | Track, study sessions, profile |
| Backend | FastAPI + Pydantic | 5 MVP endpoints, rules engine exposed via API |
| ORM / data | SQLAlchemy | Repository pattern over SQLite (dev) / Postgres (prod) |
| Auth + production database | Supabase (Auth + Postgres) | Integration target, not yet connected |
| Core loop engine | Pure Python, no I/O | SRS (adapted SM-2), mastery gate, XP/streak |

---

## Background

Project origin: the owner is a data engineer studying for Google Cloud certifications, and feels the available material is fragmented, dense, in English, and lacks habit structure. Idea: a Duolingo-style app that gamifies that studying.

Starting constraints:
- Solo developer (side project)
- Strong background in data/backend, weaker in frontend/UX
- Wants a "properly built" project: system design + TDD, not just "ship fast"
- Initial MVP ambition of ~1 month (spec was aggressively cut to fit)

---

## Documentation

| Doc | What's in it |
|---|---|
| [`docs/market-research.md`](docs/market-research.md) | Market size, competitors (CloudLearn, Whizlabs, Tutorials Dojo), the science behind Duolingo's spaced-repetition engine (HLR/SM-2/SuperMemo) |
| [`docs/product-spec.md`](docs/product-spec.md) | Consolidated MVP spec: problem, hypothesis, content scope, core loop, what's out of scope, success metric |
| [`docs/architecture-decisions.md`](docs/architecture-decisions.md) | Locked architectural premises — 100% provider/certification-agnostic modeling |
| [`docs/core-loop-srs.md`](docs/core-loop-srs.md) | Mastery and spaced-repetition engine (adapted SM-2) — formulas and locked decisions |
| [`docs/requirements.md`](docs/requirements.md) | MVP functional and non-functional requirements (FR/NFR) |
| [`docs/system-design.md`](docs/system-design.md) | Stack, architecture, database schema, endpoints, and MVP deployment plan |

---

## Running the backend locally

```bash
cd backend
uv venv .venv && uv pip install -e ".[dev]"   # or: python3 -m venv .venv && pip install -e ".[dev]"

.venv/bin/python -m pytest                     # run the suite (unit + integration)
.venv/bin/uvicorn app.main:app --reload         # start the API at http://localhost:8000/docs
```

Without `DATABASE_URL`/`SUPABASE_JWT_SECRET` set (see `backend/.env.example`), the API comes up against a local SQLite database (`backend/certfly.db`) and any authenticated route returns 500 — enough to run the test suite and develop the engine/repository, but not enough to hit the real API without a Supabase project.

### Backend structure

```
backend/app/
├── motor/       # pure business rules (SRS, mastery, XP/streak) — no I/O
├── models/      # domain entities (dataclasses) — app/models/entities.py
├── repository/  # SQLAlchemy: orm_models.py (schema), db.py (session), + 1 module per aggregate
├── services/    # orchestrates engine + repository for the 5 MVP use cases
├── routers/     # FastAPI endpoints + Pydantic request/response schemas
└── auth.py      # Supabase Auth JWT validation
```

---

## Immediate roadmap

1. Functional and non-functional requirements ✅
2. Domain modeling (entities) ✅
3. System design (stack, schema, endpoints, deploy) ✅
4. Full engine + repository + services + FastAPI API ✅
5. Flutter frontend — core loop screens implemented; full visual prototype (10 screens) ✅
6. Write real content — GCP Professional Data Engineer — next step
7. Write real content — AWS Data Engineer – Associate
8. Write real content — Azure Data Engineer Associate (DP-203)
9. Connect a real Supabase project (currently local SQLite only)

**Note (2026-08-10):** the MVP now covers 3 certifications (1 per cloud) instead of just GCP — see `docs/product-spec.md`. This delays launch (more content to write) in exchange for a stronger value proposition.
