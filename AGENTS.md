# OpenBento legacy project context

OpenBento is a free, ad-supported 12-column dashboard. Guests can use it immediately; sign-in adds cloud sync and library persistence. Preserve this product model and existing widget/layout data.

Consult `CLAUDE.md` for architecture or `replit.md` for recorded deployment context when relevant. Prefer the code when older documentation has drifted.

## Useful locations and commands

- `client/src/dashboard/dashboard-shell.tsx` owns dashboard state, pages, persistence, and cloud sync.
- `client/src/pages/dashboard.tsx` renders the grid, widget chrome, fullscreen/cast controls, and special video behavior.
- Widget types live in `client/src/widgets/shared.tsx`; renderers in `client/src/widgets/registry.tsx`; library entries in `client/src/components/widget-sidebar.tsx`.
- `shared/dashboard-pages.ts` defines page helpers and persistence keys; `shared/models/` contains Drizzle models.
- `server/routes.ts` and `server/storage.ts` own API/storage behavior. `mobile/` is the Expo companion.
- Aliases: `@/` → `client/src/`, `@shared/` → `shared/`.
- `npm run dev` starts Express/Vite on 5000; `npm run check` checks TypeScript; `npm run build` creates client/server output; `npm run start` serves it. `npm test` runs the automated tests.
- Schema tooling uses `npm run db:push`, not versioned migrations. Verify the target and authorization before applying schema changes.

## Data and product invariants

- Keep guest functionality free; do not introduce premium/payment/paywall logic without an explicit product change.
- Preserve `openBentoPages`, `openBentoActivePageId`, and the legacy `openBentoWidgets` mirror. Hydrate pages first, fall back to legacy widgets, then create an empty Home page.
- Persistence is automatic. Preserve backward-compatible defaults, JSON-serializable widget settings, and cast compatibility.
- Guests store locally. Signed-in clients hydrate once from `GET /api/dashboard`, then debounce `POST /api/dashboard` with pages, active page, and the legacy widget mirror. Failed sync must preserve local state.
- When adding a widget, keep its lowercase type, renderer, library/template entries, and needed defaults consistent. Update starter packs and command-palette helpers only when applicable.
- Keep widgets usable in small cells, mobile layouts, fullscreen, and cast contexts.

## Security and scoped work

- Keep secrets server-side and out of Git, logs, screenshots, fixtures, and browser variables.
- Preserve Supabase bearer authorization for cloud sync, admin-route authorization, public-feedback boundaries, input validation, and external-API quotas/caching.
- Keep custom-widget iframe sandboxing and postMessage validation intact.
- Preserve unrelated edits and avoid unrelated architecture/formatting changes.
- Choose checks proportional to the change: TypeScript for affected code, a build for bundling/runtime changes, and focused browser verification for risky UI behavior. Documentation-only edits do not require an application build.
- Keep production, provider, database, and destructive operations within the user's authorization.
