# Services catalog — deh-monorepo-be/apps

| App | Type | Proto (abbr) | gRPC service name | Notes |
|---|---|---|---|---|
| login-gateway | Gateway | — | — | Main BFF: auth, prelogin/postlogin, checker, dashboard, drafts, demand-draft, favourite, gst, notification, recent-activity, tableview, upcoming-transactions, workflow… |
| bbps-gateway | Gateway | — | — | BBPS: bill-payments, billers, dispute-management |
| elms-gateway | Gateway | — | — | ELMS |
| comm-cards-gateway | Gateway | — | — | Commercial cards |
| mobile-gateway | Gateway | — | — | Mobile channel |
| notification-gateway | Gateway | — | — | Notifications |
| coc-client-gateway / 2 / 3 | Gateway | — | — | TODO: why three |
| login-auth-management | Service | lams | lamService | Login, SSO session, OTP, users, JWT refresh, accounts |
| common | Service | cms | commonService | Notifications, approvals, user roles, rules, Karza GSTIN |
| beneficiary-management-service | Service | bms | TODO | Beneficiaries (+ checker) |
| dashboard | Service | dms | TODO | Dashboard data |
| mobile-service | Service | mms | TODO | Mobile features |
| payment-management-service | Service | pms | TODO | Payments |
| reports | Service | rms | TODO | Reports |
| tax-payment-service | Service | tpms | tpService | Tax/GST/challan (calls cms, srmms) |
| service-request-management | Service | srmms | srmService | Service requests, GSTIN details (Karza) |
| deposits | Service | dpms | TODO | Deposits |
| commercial-cards | Service | cmcds | TODO | Commercial cards |
| notification-service | Service | nms | TODO | Notifications |
| bbps | Service | TODO | TODO | BBPS backend |
| audit-trail | Service | TODO | TODO | Audit events |
| download | Service | TODO | TODO | File downloads |
| treasury | Service | TODO | TODO | Treasury |
| shopping-mall-aggr | Service/aggregator | TODO | TODO | Shopping mall |
| migration-scripts | Tooling | — | — | DB migrations (confirm usage) |
| mock-api | Dev tooling | — | — | Mock endpoints for local dev |

Fill the TODO cells from each proto's `service <Name> {}` line — they are needed for `getService()`/`@GrpcMethod()`.
