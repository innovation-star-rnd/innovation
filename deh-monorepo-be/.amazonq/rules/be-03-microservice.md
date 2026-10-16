# BE-03 · Microservice rules

Services own business logic and data. They are gRPC servers (and may be gRPC clients of other services).
See `memory-bank/servicesCatalog.md` for the app ↔ proto ↔ service-name map.

## Layout
```
apps/<service>/src/
  <domain>/{<domain>.controller.ts (@GrpcMethod), <domain>.service.ts,
            <domain>.repository.ts, <domain>.entity.ts, <domain>.module.ts, dto/, utils/}
  grpc-client/       # calls to OTHER services
  API_Adapters/      # integrations with external/core systems (where present)
  checker/           # maker-checker approval flows (where present)
  proto/             # copies — see BE-04
  config/, constant/, util/, mock/, main.ts, app.module.ts
```

## Controller (gRPC handlers)
```ts
@Controller()
export class GstController {
  private readonly logger = new LoggerService('tpms-gst-controller');
  constructor(private readonly gstService: GstService) {}

  @GrpcMethod('tpService', 'gstinDetails')          // ('<service in proto>', '<rpc name>')
  async gstinDetails(req: GstinDetailsRequestDto): Promise<GstinDetailsResponseDto> {
    return this.gstService.gstinDetails(req, req.correlationId);
  }
}
```
- Every `rpc` in the owning proto has exactly one matching `@GrpcMethod`.
- New endpoints in services are **gRPC only** — no new `@Get/@Post` (legacy test routes exist; don't copy).
- Pass `correlationId` (and `trackingId` when present) explicitly into service methods, as existing code does.

## Service layer
- All business rules, orchestration and transactions live here.
- Convert proto string fields to proper types here (numbers, dates via moment, booleans) and back
  to strings for responses.
- Call other services only through `grpc-client/` (never import their code, never query their tables).
- Wrap multi-step DB writes in a transaction (BE-05).
- Throw `RpcException({ code: status.X, message })` for expected failures (BE-04). For existing
  RPCs that return an error DTO in the response (e.g. `SSOErrResponseDto`), keep that contract.

## Banking-domain rules
- **Maker-checker:** the maker can never approve their own request; enforce status transitions
  server-side (don't trust the client's status); record who/when for every transition.
  Use the existing `checker` module patterns — ask before changing them.
- **Idempotency:** payment/transfer initiation must be safe to retry — dedupe on the existing
  request identifiers (requestUUID / serviceRequestId) before creating a transaction.
- **Money:** amounts travel as strings; never use JS floating-point arithmetic for them.
- **Audit:** state-changing actions should be traceable (an `audit-trail` app exists — confirm
  how events are sent before adding new ones).
- **Authorisation:** verify the user/corporate owns the account/resource in the service, not only in the gateway.

## Async & integrations
- Long-running/batch work → BullMQ jobs; scheduled work → `@nestjs/schedule` with idempotent handlers.
- 3rd-party/core-banking calls → `API_Adapters` / existing 3rd-party caller + `FetchSubHeader` (BE-01).
