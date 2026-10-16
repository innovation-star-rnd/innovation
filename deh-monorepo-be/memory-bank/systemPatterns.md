# System patterns — deh-monorepo-be

## 1. Request flow
```
deh-fe ──HTTP──► <x>-gateway
                   Controller (REST + Swagger, guards, DTO validation)
                     → <Domain>Service (orchestration)
                       → grpc-client/<abbr>.service.ts  (ClientGrpc.getService('<svcName>'))
                                │ gRPC (proto-loader, keepCase)
                                ▼
                 <service app>  @GrpcMethod('<svcName>', '<rpc>') Controller
                     → Service (business logic, transactions)
                       → Repository (TypeORM) → Postgres
                       → grpc-client → other services
                       → API_Adapters / 3rd-party caller → external systems
```

## 2. Worked example — SSO
`GET /auth/getsso` (login-gateway `AuthController.getsso`)
→ `AuthService.getsso` → `grpc-client/lams.service.ts` `LamsService.getsso`
→ gRPC `lamService.getsso` → login-auth-management `AuthController` `@GrpcMethod('lamService','getsso')`
→ `AuthService.createCdpSession(request, correlationId, trackingId)`.

## 3. Worked example — tax payment
Gateway → `tax-payment-service` (`tpms.proto` → `tpService`; GstController, ChallanService, GstService)
→ calls `common` (`cms.proto` → `commonService`: notifications, approvals, roles, rules, Karza GSTIN)
and `service-request-management` (`srmms.proto` → `srmService`: GSTIN details via Karza).

## 4. Cross-cutting
- **correlationId** is a field in every request message, passed explicitly down every layer and
  used as the first argument of every log call.
- **Logging:** `shared/utility/logger/logger.service` (`new LoggerService('<context>')`), winston-based.
- **Encryption:** `shared/utility/encryption/encryption.service` — used for the SSO flow only.
- **3rd-party calls:** the file named `errorHandling.ts` is actually the 3rd-party API caller;
  headers come from `shared/api_load.ts` `FetchSubHeader` (requestUUID, serviceRequestId,
  serviceRequestVersion, channelId).
- **Protos:** copied into each app's `src/proto/`; no canonical copy (BE-04 sync procedure).
- **Errors:** gRPC errors are not mapped consistently today; new code follows BE-04.
- **Maker-checker:** `checker` modules implement approval workflows.
