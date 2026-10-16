# Tech context — deh-monorepo-be

## Versions (from package.json)
| Area | Package | Version |
|---|---|---|
| Runtime | node / npm (engines) | >=20 <27 / >=8 <15 |
| Framework | @nestjs/core, common, microservices, platform-express | 11.1.23 |
| gRPC | @grpc/grpc-js / @grpc/proto-loader | 1.14.4 / 0.7.15 |
| DB | typeorm / @nestjs/typeorm / pg | 0.3.29 / 11.0.0 / 8.17.1 |
| Config | @nestjs/config (standard) / config (legacy) | 4.0.4 / 3.3.12 |
| Auth | @nestjs/jwt, @nestjs/passport, passport | 11.0.2 / 11.0.5 / 0.6.0 |
| API docs | @nestjs/swagger, swagger-ui-express | 11.4.4 / 4.6.3 |
| Security | helmet, @nestjs/throttler | 7.2.0 / 6.5.0 |
| Queues | bullmq, @nestjs/bullmq, ioredis | 5.61.2 / 11.0.4 / 5.8.2 |
| Search | @nestjs/elasticsearch, @opensearch-project/opensearch | 10.0.1 / 2.12.0 |
| AWS | @aws-sdk/client-secrets-manager, client-sts (v3) · aws-sdk v2 (legacy) · @aws-crypto/client-node | 3.1013.0 · 2.1693.0 · 3.2.2 |
| Validation | class-validator / class-transformer (standard) · joi, ajv (legacy) | 0.14.1 / 0.5.1 |
| Logging | winston, winston-daily-rotate-file, @opentelemetry/api-logs | 3.19.0 / 4.7.1 |
| Docs/files | handlebars, html-pdf, jspdf, exceljs, archiver, @nestjs-modules/mailer |
| Dates | moment 2.30.1, moment-timezone 0.5.48 |
| Tests | jest 29.7, ts-jest 29.4 |

## Scripts
`build` (nest build, `prebuild` rimraf dist) · `format` (prettier apps/libs) · `start`, `start:dev`,
`start:debug`, `start:prod` (`node dist/main`) · `lint` (eslint --fix) · `test`, `test:watch`,
`test:cov`, `test:e2e` · `typeorm`, `typeorm:migration:generate`, `typeorm:migration:run` · `git-hooks`.

## Root layout
`apps/` (all gateways + services), `shared/` (cross-app utilities), `config/`, `cdp-logs/`,
`.husky/` (commit-msg, pre-commit, pre-push), `.vscode/`.

## Infra
AWS EKS. Secrets via AWS Secrets Manager. Logs: winston (file rotation present — container stdout preferred).
