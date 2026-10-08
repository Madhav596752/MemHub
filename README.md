# MemHub — Frontend

MemHub is a company knowledge/memory platform. This is the frontend: an AI assistant that
answers questions with cited sources, a document library with upload + indexing status, a
knowledge graph to explore how docs/people/projects connect, and basic usage analytics.

This repo is just the UI — it currently runs on mock data (see `src/data/mockData.js`) so it
can be developed and demoed without a backend.

## Tech stack

- React 19 + Vite
- React Router for routing
- Tailwind CSS (with the shadcn/ui-style component pattern under `src/components/ui`)
- Radix UI primitives (dialog, dropdown, tooltip, etc.) under the hood of those components
- Recharts for the dashboard/analytics charts
- Sonner for toast notifications

## Project structure

```
src/
  components/     shared UI (Sidebar, TopBar, StatCard, etc.) and components/ui (button, input, ...)
  pages/          one file per route (Dashboard, AIAssistant, Documents, Search, ...)
  contexts/       ThemeContext (light/dark/system)
  data/           mockData.js — all the fake data the UI runs on right now
  lib/            small utils (cn helper)
  App.jsx         routes
  main.jsx        entry point
```

## Getting started

```bash
npm install
npm run dev
```

The app runs at `http://localhost:5173`. `/` is the marketing landing page, `/login` and
`/register` are real auth screens (Phase 1B), and everything under `/app` is the actual product
and requires login.

Other scripts:

```bash
npm run build     # production build
npm run preview   # preview the production build locally
npm run lint      # oxlint
```

## Backend (Phase 1A foundation)

A minimal Express + PostgreSQL + Prisma backend lives in `server/`. Setup:

```bash
cd server
npm install
cp .env.example .env        # then fill in DATABASE_URL, PORT, FRONTEND_URL
npx prisma generate
npx prisma migrate dev --name init
npm run dev                 # API at http://localhost:5000, health: /api/health
```

In development Vite proxies `/api/*` to `http://localhost:5000` (see `vite.config.js`).
The frontend pages still run on mock data and are not wired to the API yet.

## Authentication (Phase 1B)

Add these to `server/.env` (see `server/.env.example`):

```env
JWT_ACCESS_SECRET=<long random string>
JWT_REFRESH_SECRET=<a different long random string>
JWT_ACCESS_EXPIRES_IN=15m
JWT_REFRESH_EXPIRES_IN=7d
```

Endpoints: `POST /api/auth/register`, `POST /api/auth/login`, `POST /api/auth/refresh`,
`GET /api/auth/me` (needs `Authorization: Bearer <access-token>`).
No schema change was needed, so there is no new Prisma migration.

## Notes

- There's no backend yet — uploading a document in the Documents page just adds a fake row
  and "processes" it with a `setTimeout`.
- Documents, search, analytics and the AI assistant still run on mock data (later phases).
