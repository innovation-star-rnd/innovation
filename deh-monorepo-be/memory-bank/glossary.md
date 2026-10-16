# Glossary — CDP backend

## Service abbreviations (proto file names)
| Abbr | Meaning | App |
|---|---|---|
| lams | Login Auth Management Service | login-auth-management |
| bms | Beneficiary Management Service | beneficiary-management-service |
| cms | Common Micro Service | common |
| dms | Dashboard Micro Service | dashboard |
| mms | Mobile Micro Service | mobile-service |
| pms | Payment Micro Service | payment-management-service |
| rms | Report Micro Service | reports |
| tpms | Tax Payment Micro Service | tax-payment-service |
| srmms | Service Request Management Micro Service | service-request-management |
| dpms | Deposit Management Service | deposits |
| cmcds | Commercial Cards Service | commercial-cards |
| nms | Notification Micro Service | notification-service |

## Terms
| Term | Meaning |
|---|---|
| Gateway / BFF | HTTP entry app that calls services via gRPC |
| Service | gRPC server app that owns logic + DB |
| grpc-client | Folder in an app holding ClientGrpc wrappers for other services |
| keepCase | proto-loader option: proto field names used verbatim |
| correlationId / trackingId | Request tracing IDs carried in every message |
| shared/ | Root folder of utilities shared by all apps |
| API_Adapters | Integrations with external/core systems |
| errorHandling.ts | (Misnamed) 3rd-party API caller |
| FetchSubHeader | `shared/api_load.ts` header builder for 3rd-party calls |
| Maker / Checker | Initiator / approver in dual-control workflows (`checker` modules) |
| Karza | External provider used for GSTIN details |
| BBPS | Bharat Bill Payment System |
| GSTIN / Challan | GST tax ID / tax payment challan |
| SSO | Single sign-on flow (only place encryption is used) |
| COC | TODO: expand (coc-client-gateway) |
| ELMS | TODO: expand |
