# 🩺 Health OS — Personal Health Data Pipeline

> *Apple Watch biometrics flowing through a production-grade data engineering system — built end to end by Pawan Yandapalli.*

**[Live Dashboard →](https://pawanyandapalli7.github.io/health-tracker/)**

---

## What Is This?

Health OS is a **personal health data pipeline** built as a data engineering portfolio project. Every design decision prioritises data correctness, observability, and pipeline transparency.

It ingests raw Apple Watch biometrics, normalises and validates them through a multi-layer schema, derives recovery scores and momentum metrics via a custom intelligence engine, and surfaces everything through a real-time web dashboard and native mobile app.

This is not a todo list. It's a production-grade system running on real data — live.

---

## Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                         DATA PIPELINE                           │
│                                                                 │
│  ⌚ Apple Watch                                                  │
│       │  HealthKit sensors (HR, HRV, Sleep, Steps, SpO2...)     │
│       ↓                                                         │
│  📤 Export Layer                                                 │
│       │  Apple Shortcuts automation  →  POST /api/health        │
│       │  React Native app (direct HealthKit read)               │
│       ↓                                                         │
│  ⚡ Ingestion  [Node.js + Express]                               │
│       │  SHA-256 payload deduplication                          │
│       │  Idempotent — safe to re-run                            │
│       ↓                                                         │
│  ✅ Validation  [schema.js]                                      │
│       │  Per-field range checks                                 │
│       │  Anomaly flagging                                       │
│       │  Quality scores                                         │
│       ↓                                                         │
│  🧠 Derivation  [recovery.js + intelligence.js]                  │
│       │  Recovery score engine (HRV + sleep + HR composite)     │
│       │  Momentum score (7-day rolling completion)              │
│       │  Health intelligence layer (natural language insight)   │
│       ↓                                                         │
│  🐘 Storage  [PostgreSQL on Heroku]                              │
│       │  Raw, clean, and derived layers persisted               │
│       │  Survives all deploys                                   │
│       ↓                                                         │
│  📊 Presentation                                                 │
│       ├── Web dashboard  (GitHub Pages)                         │
│       └── React Native app  (iOS + Android)                     │
└─────────────────────────────────────────────────────────────────┘
```

---

## Tech Stack

| Layer | Technology |
|---|---|
| **Biometric Source** | Apple HealthKit (Watch Series) |
| **Backend API** | Node.js + Express |
| **Database** | PostgreSQL (Heroku Postgres) |
| **Hosting** | Heroku (API) · GitHub Pages (frontend) |
| **Mobile App** | React Native + Expo 51 |
| **State Management** | Zustand |
| **Auth** | JWT + Face ID (Expo SecureStore) |
| **Frontend** | Vanilla JS · SVG · CSS custom properties |
| **Notifications** | Expo Notifications |
| **CI/CD** | GitHub Actions (auto-deploy on push) |
| **IaC** | Heroku Procfile + Postgres provisioning |

---

## API Endpoints

| Method | Endpoint | Auth | Description |
|---|---|---|---|
| `POST` | `/api/health` | 🔒 | Ingest raw biometric snapshot |
| `GET` | `/api/health/latest` | Public | Latest clean + derived data |
| `GET` | `/api/health/history` | 🔒 | Last N days of records |
| `GET` | `/api/health/metrics` | 🔒 | Baselines, trends, training load |
| `GET` | `/api/health/insights` | 🔒 | Last 7 daily insight objects |
| `GET` | `/api/health/status` | Public | Pipeline health check |
| `POST` | `/api/login` | — | JWT authentication |

---

## Key Engineering Decisions

**Idempotent ingestion via SHA-256 dedup**
Every payload is hashed before storage. Re-sending the same data is a no-op — no duplicate records, no retries needed.

**Three-layer data model**
Raw → Clean → Derived. Each layer is independently queryable. The raw layer is immutable; cleaning and derivation are re-runnable at any time.

**Recovery score engine**
Composite metric combining HRV (40%), sleep quality (35%), and resting HR deviation (25%). Outputs a 0–100 score with green/amber/red classification and plain-English advice.

**Public / owner access split**
Visitors see live health trends, recovery status label, and 7-day averages. Exact values, personal routine, mood tracking, and full analytics are owner-only behind JWT auth.

---

## Repository Structure

```
health-tracker/               ← GitHub Pages frontend
  index.html                  ← Single-file dashboard (4600+ lines)
  manifest.json               ← PWA manifest
  sw.js                       ← Service worker (offline support)
  favicon.svg                 ← PY monogram favicon
  data.json                   ← Local task/routine data

health-os-backend/            ← Node.js API (deployed to Heroku)
  server.js                   ← Express entry point
  routes/health.js            ← All API routes
  src/
    store.js                  ← PostgreSQL data layer
    normalizer.js             ← HAE v2 field parser
    metrics.js                ← Derived metric calculations
    schema.js                 ← Validation ranges
    recovery.js               ← Recovery score engine
    intelligence.js           ← Insight generation
    logger.js                 ← Structured event log
```

---

## Dashboard Features

**Public (no login)**
- Live momentum score with trend classification
- Recovery ring — score, status label, and advice
- Health Intelligence card — daily natural language insight
- 7-day rolling averages (Sleep, Steps, HRV, HR, Workout, Calories)
- Pipeline visualization

**Owner (authenticated)**
- Full Apple Watch metrics (16 cards: Steps, Distance, HRV, Sleep stages, SpO2, Weight, and more)
- Daily routine tracker with morning / midday / night sections
- Mood and energy logging
- 30-day momentum bar chart
- Category breakdown and streak tracking
- Completion heatmap
- Personal records
- Raw data inspector with schema lineage
- Notes

---

## Data Flow (Mobile App)

```
iPhone (HealthKit)
    ↓  react-native-health reads biometrics directly
readTodaySnapshot() builds payload
    ↓  POST /api/health
Node.js normalizes + SHA-256 dedup check
    ↓  store in PostgreSQL
Recovery + intelligence engine runs
    ↓  GET /api/health/latest
App renders Recovery ring, Momentum score, Insight card
```

The mobile app **eliminates the Apple Shortcuts dependency** — HealthKit data flows directly into the pipeline without any manual trigger.

---

## Running Locally

```bash
# Backend
git clone https://github.com/pawanyandapalli7/health-tracker
cd health-os-backend
npm install
DATABASE_URL=your_postgres_url JWT_SECRET=your_secret npm start

# Frontend
# Just open index.html in a browser
# Or serve via GitHub Pages
```

---

## About

Built by **Pawan Yandapalli** — Data Engineer with 4+ years experience across AWS, Spark, dbt, Airflow, Snowflake, and Databricks.

This project demonstrates end-to-end data engineering: ingestion, validation, transformation, storage, API design, and real-time presentation — on personal biometric data.

- 🌐 [Portfolio](https://pawanyandapalli7.github.io)
- 💼 [LinkedIn](https://www.linkedin.com/in/pawanyandapalli/)
- 🐙 [GitHub](https://github.com/pawanyandapalli7)

---

<div align="center">
  <sub>Built with Node.js · PostgreSQL · React Native · Apple HealthKit · Heroku · GitHub Pages</sub>
</div>
