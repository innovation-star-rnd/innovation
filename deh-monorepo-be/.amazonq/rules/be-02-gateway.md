# BE-02 · Gateway (BFF) rules

Gateways: `login-gateway`, `bbps-gateway`, `elms-gateway`, `comm-cards-gateway`, `mobile-gateway`,
`notification-gateway`, `coc-client-gateway`, `coc-client-gateway2`, `coc-client-gateway3`.
They are the **only** entry point for HTTP traffic from the frontend.

## A gateway DOES
- Expose REST endpoints (`@Controller`, `@Get`/`@Post`) with Swagger docs.
- Authenticate/authorise (JWT/Passport guards), throttle, validate request DTOs.
- Ensure every outgoing gRPC request carries `correlationId` (and `trackingId` where used).
- Call microservices through `src/grpc-client/<abbr>.service.ts`.
- Map gRPC results/errors to HTTP responses (BE-04).

## A gateway does NOT
- Access the database / TypeORM / entities.
- Contain business rules or maker-checker decisions (those live in services).
- Call 3rd-party APIs directly in new code (legacy `axios` usage in gateway services exists — don't extend).
- Import code from another app.

## Layout
```
apps/<x>-gateway/src/
  <domain>/{<domain>.controller.ts, <domain>.service.ts, <domain>.module.ts, dto/}
  grpc-client/{<abbr>.module.ts, <abbr>.service.ts, common/…}
  proto/*.proto            # copies — see BE-04
  config/, main.ts, app.module.ts
```

## Controller template
```ts
@ApiTags('Login Auth Management Service')
@Controller('auth')
export class AuthController {
  private readonly logger = new LoggerService('coc-auth-controller');
  constructor(private readonly authService: AuthService) {}

  @Get('/getsso')
  @ApiOperation({ summary: 'Get SSO token', description: 'Generate SSO token for SSO validation' })
  @ApiResponse({ status: 200, type: SSOResponseDto })
  async getsso(@Query() query: SSORequestDto): Promise<SSOResponseDto> {
    this.logger.log(query.correlationId, 'getsso request received'); // no payload dump
    return this.authService.getsso(query);
  }
}
```
- Every endpoint: `@ApiOperation` + `@ApiResponse` with the DTO `type`. Request DTOs validated.
- Response DTO shapes are consumed by the FE — never change an existing shape silently.

## gRPC client template (new code)
```ts
interface LamsGrpcService {               // one method per rpc in lams.proto, Observable return
  getsso(req: SSORequestDto): Observable<SSOResponseDto>;
}

@Injectable()
export class LamsService implements OnModuleInit {
  private lamsGrpc!: LamsGrpcService;
  private readonly logger = new LoggerService('coc-lams-service');

  constructor(@Inject('lams') private readonly lamsClient: ClientGrpc) {}

  onModuleInit(): void {
    this.lamsGrpc = this.lamsClient.getService<LamsGrpcService>('lamService');
  }

  async getsso(req: SSORequestDto): Promise<SSOResponseDto> {
    try {
      return await lastValueFrom(this.lamsGrpc.getsso(req).pipe(timeout(GRPC_TIMEOUT_MS)));
    } catch (error) {
      this.logger.error(req.correlationId, 'lams.getsso failed', error);
      throw toHttpException(error);   // BE-04 mapping; if helper not present yet, rethrow `error`
    }
  }
}
```
- Client token (`'lams'`) and service name (`'lamService'`) must match the module registration
  and the `service` name in the proto.
- The hand-written interface must match the proto rpc names exactly (case-sensitive).
