# Project brief — CDP Backend (deh-monorepo-be)

- **Product:** Corporate Digital Platform (CDP) — corporate banking. Package `corpomni-be-microservices`.
- **Shape:** NestJS monorepo under `apps/`. HTTP **gateways** (BFF) → **gRPC** → **microservices**
  → Postgres (TypeORM), Redis/BullMQ, OpenSearch/Elasticsearch, AWS (Secrets Manager, KMS, S3).
- **Consumers:** `deh-fe` (Next.js host + MFEs) calls the gateways over HTTP.
- **Shared code:** root `shared/` folder used by all apps (logger, encryption, pipes, api_load).
- **Key domains:** login/auth & SSO, beneficiaries, payments, deposits, dashboard, reports,
  tax payments/GST, service requests, commercial cards, BBPS, notifications, mobile, treasury,
  shopping mall, audit trail.
- **Approval model:** maker-checker.
- **Deployment:** AWS EKS. Branch seen: `sit`; tickets `COP-<n>`.
- **AI tools:** Claude Code (CLAUDE.md) + Amazon Q (`.amazonq/rules/`).
