# AURA — AI Health Tracker

AI-powered nutrition tracker with a cinematic dark luxury aesthetic. Full-stack web app with food analysis, goal tracking, weekly analytics, and an AI voice assistant.

## Run & Operate

- `pnpm --filter @workspace/api-server run dev` — run the API server (port 8080, proxied at `/api`)
- `pnpm --filter @workspace/health-tracker run dev` — run the frontend (proxied at `/`)
- `pnpm run typecheck` — full typecheck across all packages
- `pnpm run build` — typecheck + build all packages
- `pnpm --filter @workspace/api-spec run codegen` — regenerate API hooks and Zod schemas from the OpenAPI spec
- `pnpm --filter @workspace/db run push` — push DB schema changes (dev only)
- Required env: `DATABASE_URL`, `SESSION_SECRET`

## Stack

- pnpm workspaces, Node.js 24, TypeScript 5.9
- Frontend: React + Vite, Wouter routing, Framer Motion, Recharts, shadcn/ui, TanStack Query
- API: Express 5
- DB: PostgreSQL + Drizzle ORM (`lib/db`)
- Validation: Zod (`zod/v4`), `drizzle-zod`
- API codegen: Orval (from OpenAPI spec in `lib/api-spec/openapi.yaml`)
- Build: esbuild (CJS bundle)

## Where things live

- `lib/api-spec/openapi.yaml` — OpenAPI contract (source of truth)
- `lib/api-zod/src/generated/api.ts` — Zod schemas (generated)
- `lib/api-client-react/src/generated/api.ts` — React Query hooks (generated)
- `lib/db/src/schema/` — DB tables: users, meals, notifications
- `artifacts/api-server/src/routes/` — All API routes: auth, users, meals, goals, analytics, recommendations, notifications
- `artifacts/health-tracker/src/pages/` — All pages: login, signup, onboarding, dashboard, analysis, goals, analytics, notifications
- `artifacts/health-tracker/src/components/` — Navbar, VoiceAssistant, shadcn/ui components

## Architecture decisions

- JWT stored in localStorage; tokens kept in server-side in-memory Map (production: use Redis)
- Auth middleware (`requireAuth`) in `artifacts/api-server/src/routes/auth.ts` — import into every protected route
- Food analysis uses a keyword lookup table (no external AI API needed); easily swappable for OpenAI
- All pages use Framer Motion for animations; design system uses CSS custom properties in `index.css`
- DB schema uses Drizzle ORM with `drizzle-kit push` for dev migrations

## Product

- Login / Signup with JWT auth
- Onboarding: age group, diet style, goal, deadline
- Dashboard: 4 meal periods (morning/afternoon/evening/night) with food logging and AI recommendations
- AI Food Scanner: describe any food in natural language → instant macro breakdown
- Goals page: radial progress rings, deadline countdown, AI forecast
- Analytics page: weekly charts (Recharts), health risk assessment, 30-day forecast
- Notifications: categorized alerts with mark-as-read
- AI Voice Assistant: floating orb, accepts natural language commands to log food

## Design System

- Background: `#050505`, Cards: `rgba(255,255,255,0.03)`
- Gold accent: `#D6B36A` (`--primary` in CSS)
- Muted text: `#9CA3AF`
- Fonts: Space Grotesk (sans) + DM Serif Display (serif)
- Utility classes: `.glass-card`, `.glass-card-hover`, `.gold-glow`, `.ambient-bg`

## User preferences

_Populate as you build — explicit user instructions worth remembering across sessions._

## Gotchas

- Always run `pnpm --filter @workspace/api-spec run codegen` after changing `openapi.yaml`
- Always run `pnpm run typecheck:libs` before typechecking leaf packages if DB schema changes
- Never use `console.log` in server code — use `req.log` or `logger`
- Token store is in-memory: server restart clears all sessions (users must log in again)

## Pointers

- See the `pnpm-workspace` skill for workspace structure, TypeScript setup, and package details
