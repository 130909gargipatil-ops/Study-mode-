# StudyMode — JEE & Boards Study Tracker

A full-featured personal study OS for JEE (11th + 12th) and Boards (12th) preparation. Gamified, themed, and built to make studying feel powerful.

## Run & Operate

- `pnpm --filter @workspace/api-server run dev` — run the API server (port 8080)
- `pnpm --filter @workspace/jee-tracker run dev` — run the frontend (port 24028)
- `pnpm run typecheck` — full typecheck across all packages
- `pnpm run build` — typecheck + build all packages
- `pnpm --filter @workspace/api-spec run codegen` — regenerate API hooks and Zod schemas from the OpenAPI spec
- `pnpm --filter @workspace/db run push` — push DB schema changes (dev only)
- Required env: `DATABASE_URL` — Postgres connection string

## Stack

- pnpm workspaces, Node.js 24, TypeScript 5.9
- Frontend: React + Vite + TailwindCSS + Framer Motion + Recharts
- API: Express 5
- DB: PostgreSQL + Drizzle ORM
- Validation: Zod (`zod/v4`), `drizzle-zod`
- API codegen: Orval (from OpenAPI spec)
- Build: esbuild (CJS bundle)

## Where things live

- DB schema: `lib/db/src/schema/` (one file per entity)
- API contract: `lib/api-spec/openapi.yaml`
- Generated hooks: `lib/api-client-react/src/generated/`
- Generated Zod schemas: `lib/api-zod/src/generated/`
- API routes: `artifacts/api-server/src/routes/`
- Frontend pages: `artifacts/jee-tracker/src/pages/`
- Themes: CSS variables in `artifacts/jee-tracker/src/index.css`, controlled via `data-theme` on `<html>`

## Architecture decisions

- OpenAPI-first: All API contracts defined in `lib/api-spec/openapi.yaml` and generated via Orval
- SM-2 spaced repetition: Implemented in `artifacts/api-server/src/routes/reviews.ts`
- AI planner: Rule-based chat in `artifacts/api-server/src/routes/ai.ts` — no external API needed
- Themes: Stored in `localStorage` key `studymode-theme`, applied as `data-theme` attribute on `<html>`
- Settings singleton: `user_settings` table has one row, auto-created if missing

## Product

- Dashboard with XP, level, countdowns, upcoming events/tests, due reviews, tasks
- JEE + Boards syllabus tracker (chapter cards with confidence, oneshot, completion)
- Chapter detail pages with notes, test marks, spaced repetition entry
- Calendar + visual time blocker (Google Calendar style)
- Task database with kanban/list view
- Habit tracker with streaks and heatmap
- Spaced repetition (SM-2 algorithm: 1→3→7→14→30 day intervals)
- JEE test analysis (physics/chem/maths breakdown, percentile, trends)
- Weekly & monthly goal setter with progress bars
- Vision board with image URLs
- AI study planner "JARVIS" (day planning, motivation, revision strategy)
- Multiple themes: Dark Tech (default), Iron Man, Bang Chan, Hyunjin

## User preferences

_Populate as you build — explicit user instructions worth remembering across sessions._

## Gotchas

- Body schema naming: OpenAPI component names must NOT match `<OperationIdPascal>Body` — use entity-shaped names (e.g. `ChapterInput` not `CreateChapterBody`) to avoid TS2308 collisions in the generated Zod barrel
- Query params on routes can also cause TS2308 — if a GET endpoint has query params, Orval generates `<OperationPascal>Params` in both `generated/api.ts` and `generated/types/`. Avoid complex query-param objects when the name would collide.
- DB `date` columns use `mode: "string"` to preserve YYYY-MM-DD format without timezone shifts

## Pointers

- See the `pnpm-workspace` skill for workspace structure, TypeScript setup, and package details
