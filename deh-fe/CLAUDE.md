# CDP Frontend (deh-fe) — AI entry point

Corporate Digital Platform **host shell** (Next.js 16, Pages Router, React 19, TypeScript) that loads
feature **micro-frontends (MFEs)** at runtime from UMD bundles.

## Rules (always loaded — Amazon Q reads the same files from .amazonq/rules)
@.amazonq/rules/fe-00-ai-workflow.md
@.amazonq/rules/fe-01-typescript-react.md
@.amazonq/rules/fe-02-feature-mfe.md
@.amazonq/rules/fe-03-bundling-build.md
@.amazonq/rules/fe-04-pages-routing.md
@.amazonq/rules/fe-05-state-redux-saga.md

## Memory bank (always loaded: brief + glossary)
@memory-bank/projectBrief.md
@memory-bank/glossary.md

## Memory bank (read on demand — open the file when the task touches it)
- `memory-bank/systemPatterns.md` — MFE architecture, runtime loading, layer contract, stores
- `memory-bank/techContext.md` — versions, scripts, env files, infra folders
- `memory-bank/featureCatalog.md` — feature folders ↔ bundle scripts ↔ webpack configs
- `memory-bank/decisions-and-tech-debt.md` — standard library choices, legacy to avoid
- `memory-bank/activeContext.md` — what the team is working on right now

## Commands
- Dev (Turbopack, port 5000): `npm run dev`
- Prod build (webpack): `npm run build`
- Lint: `npm run lint` · Tests: `npm test` / `npm run test-cov`
- Build one MFE + copy to public/assets: e.g. `npm run bds` (dashboard), `npm run bp` (payments)
- Build one MFE only: `npm run bundle-<feature>` (see featureCatalog)
