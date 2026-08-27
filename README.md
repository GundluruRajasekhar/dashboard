# DevPulse — Developer Productivity Dashboard

A polyglot dashboard for tracking developer productivity: tasks, focus
sessions, GitHub activity, and a computed productivity score — built to
show React/Next.js, Node.js, Python, MongoDB, and MySQL working together
in one deployable project.

## Stack & where each language lives

| Layer | Tech | Where |
|---|---|---|
| UI | React + Next.js | `/pages`, `/components` |
| API (Node.js) | Next.js API routes | `/pages/api/*.js` |
| Analytics scoring | Python | `/api/analytics.py` (Vercel serverless function) |
| Live data | MongoDB (Mongoose) | `/models`, `/lib/mongodb.js` |
| Historical data | MySQL | `/sql/schema.sql`, `/lib/mysql.js` |

**Why two databases?** MongoDB stores the live, flexible task and
focus-session records. MySQL stores the rolled-up daily history used
for the trend chart — a structured, relational log that's cheap to
query and report on. This mirrors a real setup where operational data
and reporting data live in different stores.

## Features

- Task board (create, update status, delete) — MongoDB
- Focus session timer that logs completed sessions — MongoDB
- 14-day productivity trend chart — MySQL
- GitHub activity widget (recent commits, merged PRs, repos) — GitHub REST API
- Productivity score (0–100) computed by a Python serverless function

## Local setup

```bash
npm install
cp .env.example .env.local   # fill in MONGODB_URI at minimum
npm run dev
```

Open http://localhost:3000.

MySQL and GitHub are optional locally — the dashboard degrades
gracefully (empty chart, "no data") if they're not configured.

### Optional: MySQL history

```bash
mysql -u <user> -p < sql/schema.sql
npm run seed:mysql   # populates 14 days of sample history
```

## Deployment

See [`docs/DEPLOYMENT.md`](docs/DEPLOYMENT.md) for the exact
GitHub → Vercel steps.

## Project structure

```
devpulse/
├── api/analytics.py           # Python serverless function (Vercel)
├── pages/
│   ├── index.js                # Dashboard UI
│   └── api/                    # Node.js API routes
│       ├── tasks.js
│       ├── focus-sessions.js
│       ├── metrics.js
│       └── github-activity.js
├── components/                 # React UI components
├── models/                      # Mongoose schemas
├── lib/                          # MongoDB + MySQL connection helpers
├── sql/schema.sql               # MySQL schema
├── scripts/seedMysql.js        # Sample data seeder
└── vercel.json                  # Registers the Python function
```
