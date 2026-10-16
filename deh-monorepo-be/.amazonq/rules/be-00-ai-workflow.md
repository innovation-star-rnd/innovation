# BE-00 · How the AI must work in deh-monorepo-be

## Before writing code
1. Identify the app and its type using `memory-bank/servicesCatalog.md`:
   **gateway** (`*-gateway`, HTTP entry) or **service** (gRPC server, owns DB). Rules differ (BE-02 / BE-03).
2. Find the closest existing module in the **same app** and mirror it (folder layout, DTO style,
   logger context, grpc-client usage).
3. For any new/changed RPC, follow the end-to-end checklist in BE-04 — it always touches more than one app.
4. For changes touching more than 2 files, list the files and the reason first.
5. Never invent an RPC, proto field, table, column, env/config key or 3rd-party endpoint — ask.

## While writing code
- Smallest change that solves the task. Don't reformat or refactor unrelated code.
- **Don't rename existing RPCs, methods, DTO fields or proto fields — even typos** (`getsso`,
  `resuest`, …). They are wire contracts (proto loader uses `keepCase`).
- No new npm dependency without asking; check `decisions-and-tech-debt.md` first.
- Don't touch `node_modules`, `dist`, `cdp-logs`.

## Protected — ask before editing
`shared/**` (used by every app), any `*.proto`, `main.ts`, `microservice.config.ts`,
`config/**`, `app.module.ts` imports of global modules, migrations, `package.json`
(deps/overrides/scripts), `nest-cli.json`, `tsconfig*.json`, `.husky/**`, Docker/Helm/K8s files,
anything under `checker` (maker-checker) or auth/JWT/SSO/encryption.

## After writing code
- Run `npm run lint` and the relevant tests; for proto changes run every affected app locally.
- Summarise: files changed per app, contract changes, what you couldn't verify.

## Commits
`COP-<ticket> FEAT: <summary>` (husky commit-msg/pre-commit/pre-push hooks run — don't bypass with `--no-verify`).
