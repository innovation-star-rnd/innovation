# BE-01 · TypeScript & NestJS coding rules

## TypeScript
- No `any` in new code (use DTO classes, interfaces, generics, `unknown` + narrowing).
- Public methods declare return types (`Promise<XxxResponseDto>`).
- `async/await` only; no floating promises; no mixing `.then()` chains in new code.
- Injected dependencies are `private readonly`.
- Use `?.` / `??`; never `||` for defaults on amounts/flags.

## NestJS structure (per domain folder)
```
src/<domain>/
  <domain>.module.ts
  <domain>.controller.ts   # gateway: REST · service: @GrpcMethod handlers
  <domain>.service.ts      # logic (gateway: orchestration only)
  <domain>.repository.ts   # services only (TypeORM)
  <domain>.entity.ts       # services only
  dto/                     # request/response DTO classes
  utils/
```
- Constructor DI only; never `new` a provider (exception: the existing logger pattern below).
- Keep controllers thin: validate → call service → return.
- Domain modules export only what other modules need.

## Configuration & secrets
- Use `@nestjs/config` `ConfigService`. Don't add new `require('config')` usages (legacy).
- Secrets come from AWS Secrets Manager via the existing helper — never hard-code or commit them.
- AWS: **SDK v3** modular clients (`@aws-sdk/client-*`). No new `aws-sdk` (v2) imports.

## Validation
- `class-validator` + `class-transformer` decorators on every request DTO
  (`@IsString()`, `@IsNotEmpty()`, `@Matches()`, `@ValidateNested()` + `@Type()`…).
- Don't use `joi` / `ajv` in new code.
- Reuse pipes from `shared/pipes` (e.g. non-empty-parameter validation) where they fit.

## Logging (shared LoggerService)
- Pattern: `private readonly logger = new LoggerService('<app>-<layer>');`
  e.g. `'coc-auth-controller'`, `'coc-lams-service'`.
- **First argument is always the `correlationId`**: `this.logger.log(req.correlationId, 'msg', meta)`.
- Levels: `error` failures, `warn` recoverable, `log` business milestones, `debug/silly` dev detail.
- **Never log**: passwords, OTPs, JWT/session tokens, encryption keys, full account/card numbers,
  PAN/Aadhaar/GSTIN payloads, full request bodies (`JSON.stringify(req)`), encrypted blobs.
  Log IDs and masked values instead.

## Async/RxJS
- Convert gRPC Observables with `lastValueFrom()` / `firstValueFrom()` — **not** `.toPromise()` (deprecated).

## Errors
- Never swallow errors; never `throw new Error('error : ' + error)` (loses code, prints `[object Object]`).
- Gateways throw Nest `HttpException` subclasses; services throw `RpcException` with a gRPC status
  code (see BE-04). Don't return stack traces or internal messages to clients.

## Imports & sharing
- **No cross-app relative imports** (`../../../notification-service/src/dto/...`). Shared code goes
  in `shared/` (ask first), or DTOs are duplicated per app matching the proto.
- Import shared utilities from `shared/utility/*` (logger, encryption) and `shared/pipes/*`.

## Third-party HTTP calls
- Go through the existing 3rd-party caller (`errorHandling.ts` — misnamed; it is the API caller)
  and build headers with `shared/api_load.ts` (`FetchSubHeader`: requestUUID, serviceRequestId,
  serviceRequestVersion, channelId). Don't call `axios` directly in new code.
- Always set a timeout; log request IDs, not payloads.

## Other standards
- Dates: `moment` / `moment-timezone`. Money: never float maths — keep as string/decimal.
- Jobs/queues: BullMQ (`@nestjs/bullmq`) + Redis; schedules: `@nestjs/schedule`.
- Gateway hardening already present: helmet, compression, throttler, JWT/Passport — keep them on new routes.

## Testing
- Jest + ts-jest; files `*.spec.ts` next to the code (or the app's `test/` for e2e).
- Unit-test services with mocked `ClientGrpc` services and repositories (`useValue` providers).
- Every new RPC/endpoint: at least happy path + validation failure + downstream error.
