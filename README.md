# CDP — AI coding rules & memory bank

Rules and memory bank for **Claude Code** and **Amazon Q**, kept separate for the two repos.

```
cdp-ai-rules/
├── deh-fe/                         → copy into the root of the deh-fe repo
│   ├── CLAUDE.md                   Claude Code entry (imports the rules + key memory files)
│   ├── .amazonq/rules/             Amazon Q auto-loads every .md here
│   │   ├── fe-00-ai-workflow.md        how the AI must work, protected files
│   │   ├── fe-01-typescript-react.md   TS / React 19 / UI / security / testing
│   │   ├── fe-02-feature-mfe.md        feature folder contract (library → component → organism)
│   │   ├── fe-03-bundling-build.md     next.config, Turbopack vs webpack, MFE bundles, externals
│   │   ├── fe-04-pages-routing.md      Pages Router shell, catch-all, SSO pages
│   │   └── fe-05-state-redux-saga.md   per-MFE stores, saga data flow
│   └── memory-bank/
│       projectBrief · techContext · systemPatterns · featureCatalog · glossary
│       decisions-and-tech-debt · activeContext
└── deh-monorepo-be/                → copy into the root of the deh-monorepo-be repo
    ├── CLAUDE.md
    ├── .amazonq/rules/
    │   ├── be-00-ai-workflow.md        how the AI must work, protected files
    │   ├── be-01-typescript-nestjs.md  TS / Nest / config / validation / logging / errors
    │   ├── be-02-gateway.md            gateway responsibilities + templates
    │   ├── be-03-microservice.md       service layout, @GrpcMethod, banking rules
    │   ├── be-04-grpc-proto.md         proto contract, sync procedure, error mapping
    │   └── be-05-database-typeorm.md   TypeORM/Postgres, transactions, migrations
    └── memory-bank/
        projectBrief · techContext · systemPatterns · servicesCatalog · glossary
        decisions-and-tech-debt · activeContext
```

## Install
1. Copy each folder's contents into the matching repo root (merge `.amazonq/` if it exists).
2. Commit them so the whole team shares the same rules.
3. **Claude Code:** run it from the repo root; `/memory` (or `/context`) should list CLAUDE.md and the
   imported rule files. One set of files serves both tools — CLAUDE.md imports `.amazonq/rules/*`.
4. **Amazon Q:** open the repo; rules in `.amazonq/rules/` apply automatically (check the **Rules**
   button in the chat panel). For memory-bank context, add `@memory-bank/<file>.md` to the prompt
   or ask Q to read it.

## How developers should prompt
- Name the place: *"In `features/cdx-payments` single maker flow, add …"* / *"In `tax-payment-service`, add rpc …"*.
- Ask for a plan first on multi-file work: *"Plan first, list files, then wait."*
- For BE contract changes: *"Follow the BE-04 add-RPC checklist."*
- End a session with: *"Update memory-bank/activeContext.md."*

## Keep it healthy
- Rules load into every session — keep them short; put facts in the memory bank, not the rules.
- When a review catches something the AI got wrong, add one line to the relevant rule file.
- Update `featureCatalog.md` / `servicesCatalog.md` when apps, features or scripts are added.
- Review `decisions-and-tech-debt.md` each release.

## Please confirm / fill in (marked TODO or "confirm" in the files)
**Frontend**
- [ ] Full `features/` list and feature-folder ↔ bundle mapping (featureCatalog).
- [ ] Where a new module is registered for the host (menuConfig.ts vs route map) — fe-02, fe-04.
- [ ] Purpose of `pages/api/*`.
- [ ] Release rule for bumping `CDP-Branding/version/version-config.json`.
- [ ] `@dnd-kit` as the DnD standard.
- [ ] ELMS / Payvantage descriptions (glossary).

**Backend**
- [ ] gRPC service names for bms, dms, mms, pms, rms, dpms, cmcds, nms (servicesCatalog).
- [ ] Type/proto of audit-trail, bbps, download, treasury, shopping-mall-aggr; why 3 coc-client-gateways.
- [ ] Error-mapping standard + shared `toHttpException` helper (be-04) and gRPC client timeout value.
- [ ] Current migration workflow (TypeORM 0.3 vs 0.2-style scripts, `apps/migration-scripts`).
- [ ] Whether services share one Postgres database or own separate schemas.
- [ ] How audit events reach `audit-trail`.
- [ ] COC / ELMS expansions (glossary).
