# CDP Backend (deh-monorepo-be) — AI entry point

NestJS 11 monorepo (`corpomni-be-microservices`). HTTP **gateways** receive REST calls and call
**microservices** over **gRPC**. Postgres via TypeORM. Deployed on AWS EKS.

## Rules (always loaded — Amazon Q reads the same files from .amazonq/rules)
@.amazonq/rules/be-00-ai-workflow.md
@.amazonq/rules/be-01-typescript-nestjs.md
@.amazonq/rules/be-02-gateway.md
@.amazonq/rules/be-03-microservice.md
@.amazonq/rules/be-04-grpc-proto.md
@.amazonq/rules/be-05-database-typeorm.md

## Memory bank (always loaded: brief + glossary)
@memory-bank/projectBrief.md
@memory-bank/glossary.md

## Memory bank (read on demand)
- `memory-bank/servicesCatalog.md` — every app: gateway/service, proto, gRPC service name
- `memory-bank/systemPatterns.md` — request flow, correlationId, shared/, 3rd-party calls
- `memory-bank/techContext.md` — versions, scripts, folders, infra
- `memory-bank/decisions-and-tech-debt.md` — standard choices, legacy to avoid
- `memory-bank/activeContext.md` — current work

## Commands
- Build: `npm run build` (nest build) · Format: `npm run format` · Lint: `npm run lint`
- Run one app: `npx nest start <app-name> --watch`
- Tests: `npm test`, `npm run test:cov`, `npm run test:e2e`
- Migrations: see be-05 (confirm the working process before using the scripts)
