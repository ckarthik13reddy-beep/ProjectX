# Deploying ProjectX on Vercel (stable baseline)

This repo is configured for a **single Vercel container service** (`app`) so runtime behavior stays close to local `pnpm dev` usage while remaining production-safe.

## 1) Vercel project shape

Root `vercel.json` uses one service:

- service: `app`
- runtime: `container`
- public route: `/(.*)` -> `app`

This avoids split-runtime mismatches (`/api` on one service, app shell on another).

## 2) Required environment variables

Use `.env.vercel.example` as the source of truth and set variables in:

- **Production**
- **Preview**

Minimum required:

- `DATABASE_URL` (must be `postgres://` or `postgresql://`)
- `BETTER_AUTH_SECRET`
- `PAPERCLIP_TOOL_ACTION_SIGNING_SECRET`
- `SERVE_UI=true`

## 3) Why this differs from local dev

Local dev can run with embedded DB when `DATABASE_URL` is absent. Public authenticated deployments do **not** allow that fallback and require external Postgres.

## 4) Deploy steps

1. In Vercel, import `ckarthik13reddy-beep/ProjectX`.
2. Confirm Vercel reads root `vercel.json`.
3. Add env vars from `.env.vercel.example`.
4. Deploy.

## 5) Post-deploy checks

After first deploy, verify:

- `GET /api/health` returns `status: ok`
- UI loads on `/`
- You can create/select a company in the UI

## 6) Scaling guidance (safe defaults)

- Start with one container instance until first successful smoke checks.
- Scale horizontally only after confirming DB pool sizing for your provider.
- Keep app state external (DB/object storage); container disk is ephemeral.

## 7) If deployment fails

Common causes:

- missing/invalid `DATABASE_URL`
- missing `BETTER_AUTH_SECRET`
- missing `PAPERCLIP_TOOL_ACTION_SIGNING_SECRET`

Fix env vars and redeploy.
