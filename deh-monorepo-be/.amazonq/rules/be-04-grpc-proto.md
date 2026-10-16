# BE-04 · gRPC & proto rules

## How it works here
- Transport: `@nestjs/microservices` + `@grpc/grpc-js`; protos loaded at runtime by
  `@grpc/proto-loader` (no code generation). TS interfaces/DTOs are **hand-written** and must mirror the proto.
- Loader option `keepCase: true` (in `microservice.config.ts`): field names stay exactly as written
  in the proto (snake_case stays snake_case). DTO property names must match character-for-character.
- **The proto is the contract.** Every `rpc` ⇄ one `@GrpcMethod('<service>', '<rpc>')` in the owner;
  every `message` defines the exact request/response shape — unknown fields don't pass through.

## Message conventions
- proto3. Scalar fields are **`string`** by convention (amounts, dates, IDs, flags); convert in the service layer.
- Every request message carries `correlationId` (+ `trackingId` where the flow uses it).
- Use `optional` for new fields where presence must be detected.
- Use `repeated` for lists, nested `message` for structures — no JSON strings inside strings for new fields.

## Backward compatibility (non-negotiable)
- Never change or reuse a field **number**; never change a field's **type**.
- Never rename a field or rpc (keepCase makes names part of the wire object). Add a new one instead.
- Remove a field only by marking it `reserved` (number and name), after all consumers stop using it.
- Removing an rpc requires every caller to be removed first.

## No single source of truth — keep copies in sync
The same proto (e.g. `lams.proto`, `bms.proto`, `cms.proto`) is **copied** into the `src/proto/`
of the owning service and every gateway/service that calls it. When changing a proto:
1. Change it in the **owning service** first.
2. Find every copy: `find apps -name "<abbr>.proto" -not -path "*/node_modules/*"`.
3. Apply the identical change to each; verify they match: `md5sum $(find apps -name "<abbr>.proto" -not -path "*/node_modules/*")`.
4. Update each consumer's hand-written `<Abbr>GrpcServicesInterface` and DTOs.
(`api_loads.ts` inside some `proto/` folders is unused — ignore it.)

## End-to-end checklist: add a new RPC
1. Owner service proto: add `rpc` + request/response `message`s; sync all copies (above).
2. Owner service: DTO classes, `@GrpcMethod` handler, service method, repository changes, tests.
3. Calling gateway: interface method in `grpc-client/<abbr>.service.ts` + wrapper method with
   `lastValueFrom` + timeout + error mapping.
4. Gateway: request/response DTOs, service method, controller endpoint + Swagger, tests.
5. If another service is also a consumer, repeat step 3 there.

## Error mapping (standard for NEW code — errors are not handled consistently today)
Service side: `throw new RpcException({ code: status.NOT_FOUND, message: 'Beneficiary not found' })`
(`status` from `@grpc/grpc-js`). Gateway side: map to HTTP, never forward internal details.

| gRPC status | HTTP |
|---|---|
| INVALID_ARGUMENT, FAILED_PRECONDITION, OUT_OF_RANGE | 400 |
| UNAUTHENTICATED | 401 |
| PERMISSION_DENIED | 403 |
| NOT_FOUND | 404 |
| ALREADY_EXISTS, ABORTED | 409 |
| RESOURCE_EXHAUSTED | 429 |
| UNIMPLEMENTED | 501 |
| UNAVAILABLE | 503 |
| DEADLINE_EXCEEDED | 504 |
| INTERNAL, UNKNOWN, others | 500 |

Implement this once as a shared helper (`toHttpException(error)` in `shared/`) — ask before creating it;
until it exists, rethrow the original error rather than wrapping it in a string.

## Client-side reliability
- Always `lastValueFrom(obs.pipe(timeout(ms)))` on new client calls; no `.toPromise()`.
- Don't retry non-idempotent RPCs (payments, approvals) automatically.
- `correlationId` stays a message field (not gRPC metadata) unless the team decides otherwise.
