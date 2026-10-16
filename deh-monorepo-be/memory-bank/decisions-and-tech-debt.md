# Decisions & tech debt — deh-monorepo-be

## Standard choices for NEW code
| Area | Decision | Status |
|---|---|---|
| Validation | class-validator + class-transformer | Agreed |
| Config | @nestjs/config ConfigService | Agreed |
| AWS SDK | v3 modular clients | Agreed |
| Dates | moment / moment-timezone | Agreed |
| Observable → Promise | lastValueFrom / firstValueFrom | Recommended |
| gRPC errors | RpcException + status codes, mapped in gateway (BE-04 table) | Proposed — confirm |
| Client timeouts | rxjs `timeout()` on every new gRPC call | Proposed — confirm |
| Encryption | SSO flow only | Agreed |
| Shared code | `shared/` — no cross-app imports | Agreed |

## Known tech debt (don't extend; fix only when asked)
- Protos copied per app, no single source of truth (candidate: move to `shared/proto/`).
- gRPC errors not handled; `throw new Error('error : ' + error)` in grpc-clients.
- `.toPromise()` usage (deprecated RxJS API).
- Cross-app relative imports (e.g. `../../../notification-service/src/dto/...`).
- `require('config')` alongside `@nestjs/config`.
- aws-sdk v2 alongside v3; joi/ajv alongside class-validator.
- Logging full request objects (`JSON.stringify(req)`) in some controllers.
- Legacy HTTP test routes inside gRPC services (e.g. `@Get('/id')`).
- Direct axios calls in gateway services.
- Migration scripts use TypeORM 0.2-style flags with TypeORM 0.3.
- File-based log rotation inside containers (EKS prefers stdout).
- jest/ts-jest listed under `dependencies`.
- `api_loads.ts` in some `proto/` folders — unused.
