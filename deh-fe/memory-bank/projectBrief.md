# Project brief — CDP Frontend (deh-fe)

- **Product:** Corporate Digital Platform (CDP) — corporate banking web portal
  (package name `corporate-digital-platform`).
- **Repos:** `deh-fe` (this repo, Next.js host + feature MFEs) and `deh-monorepo-be`
  (NestJS gRPC microservices, `corpomni-be-microservices`). FE calls the BE **gateways** over HTTP.
- **Architecture:** Next.js Pages Router host shell that loads feature micro-frontends
  (`features/cdx-*`) at runtime as UMD bundles, each with its own Redux + saga store.
- **Business areas:** payments (single/bulk maker, beneficiaries), dashboard, deposits, drafts,
  reports, treasury, GST / tax e-payment, BBPS bill payments, commercial cards, service requests,
  account manager, shopping mall, Payvantage, ELMS, pre-login/SSO.
- **Approval model:** maker-checker workflows.
- **Branches / tickets:** release branches `release/vX.Y.Z`; tickets `COP-<n>`.
- **AI tools used:** Claude Code (CLAUDE.md) and Amazon Q (`.amazonq/rules/`).
