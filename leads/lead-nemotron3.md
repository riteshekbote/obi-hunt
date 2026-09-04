## 2026-09-03 17:18:06 UTC [target] (model nemotron3)
## 2026-09-03 20:01:37 UTC [target] (model nemotron3)
[PRIO] api.live.app.obi.de,8.1,attack_surface=8(mobile API v1),business_value=9(customer auth/data),tech_exposure=8(Envoy/versioned),gate_ease=6(needs enum),cloud_surface=7(Envoy),freshness=8(active)
[PRIO] assets.obi.de,7.6,attack_surface=7(S3 CDN + seller JS),business_value=8(seller onboarding),tech_exposure=8(CORS:*+exposed JS),gate_ease=9(public),cloud_surface=8(S3),freshness=7(recent bundle)
[PRIO] imgix.obi.de,6.8,attack_surface=6(image CDN),business_value=6(media),tech_exposure=7(CORS:* S3),gate_ease=10(public),cloud_surface=7(S3),freshness=6(standard)
[PRIO] obi-de.app.baqend.com,6.2,attack_surface=5(BaaS),business_value=7(speed kit data),tech_exposure=5(unknown),gate_ease=5(needs auth),cloud_surface=6(BaaS),freshness=6(active)
[PRIO] www.obi.de,8.5,attack_surface=9(multi-API),business_value=10(e-comm 10M),tech_exposure=8(Vtex/JWT),gate_ease=4(browser UA only),cloud_surface=8(CF+Baqend),freshness=8(Vtex 2024)
[PRIO] api.obi.com,9.2,attack_surface=10(14+ APIs),business_value=9(marketplace),tech_exposure=9(MuleSoft/CORS:*),gate_ease=10(public),cloud_surface=8(CF+MuleSoft),freshness=9(seller portal)
[HYP] Mobile API Versioned Endpoint Enumeration & Auth Bypass
class: AUTH
asset: api.live.app.obi.de
confidence: 70
reasoning: NEW asset, Envoy proxy with /v1/ versioned API path. Mobile app APIs often have weaker auth gates than web. Versioned paths (/v1/, /v2/) suggest legacy endpoints may persist without auth updates. No probes executed yet.
evidence_needed: Confirm /v1/ base path responds; enumerate sub-paths (/v1/auth, /v1/user, /v1/orders); test auth requirements per endpoint
verify_steps: GET https://api.live.app.obi.de/v1/ → GET https://api.live.app.obi.de/v1/health → GET https://api.live.app.obi.de/v1/auth/login → OPTIONS https://api.live.app.obi.de/v1/ (CORS) → test POST /v1/auth/login with empty body
impact: Full customer account access, order history, payment methods, PII for mobile app users. Severity: HIGH.
testability: PASSIVE
[HYP] Seller Onboarding JS Bundle Secrets & Hardcoded Endpoints
class: MISCONFIG
asset: assets.obi.de
confidence: 80
reasoning: NEW finding — seller-side-panel/resources/index-BUGS3Fny.js served from S3-backed CDN with no auth gate. Frontend bundles often contain API keys, internal endpoints, auth token formats, or seller portal URLs. CORS: * on parent domain enables cross-origin read.
evidence_needed: Extract and analyze JS bundle for hardcoded secrets, API endpoints, auth logic, seller portal URLs, token storage patterns
verify_steps: GET https://assets.obi.de/seller-side-panel/resources/index-BUGS3Fny.js → static analysis for endpoints/keys/tokens → check for MuleSoft API URLs, JWT handling, seller dashboard paths → verify any found endpoints respond
impact: Exposure of seller onboarding flow, internal API topology, potential API keys, auth bypass vectors for seller portal. Severity: HIGH.
testability: PASSIVE
[HYP] Baqend BaaS Speed Kit Data Exposure via Misconfigured Permissions
class: IDOR
asset: obi-de.app.baqend.com
confidence: 55
reasoning: NEW asset — Baqend BaaS (Backend-as-a-Service) speed kit integration. BaaS platforms often expose data APIs with object-level permissions. If speed kit caches customer/session data with predictable IDs, cross-user data access possible. No auth requirement confirmed.
evidence_needed: Confirm Baqend app responds; identify data collections (users, sessions, carts); test IDOR on object IDs
verify_steps: GET https://obi-de.app.baqend.com/ → GET https://obi-de.app.baqend.com/api/ → check for /db/ or /api/v1/ paths → test GET /api/v1/users/1, /api/v1/sessions/{id} with incrementing IDs
impact: Customer session data, cart contents, PII leakage across 10M+ users. Severity: MEDIUM-HIGH.
testability: PASSIVE
[PARKED] Baqend BaaS Speed Kit Data Exposure via Misconfigured Permissions: confidence 55 but Baqend platform specifics unknown; verify steps generic; may require auth from start; lower priority vs confirmed exposed assets
[FINAL] 1. Seller Onboarding JS Bundle Secrets & Hardcoded Endpoints (assets.obi.de, confidence 80, PASSIVE, HIGH impact)
[FINAL] 2. Mobile API Versioned Endpoint Enumeration & Auth Bypass (api.live.app.obi.de, confidence 70, PASSIVE, HIGH impact)
[FINAL] 3. Baqend BaaS Speed Kit Data Exposure via Misconfigured Permissions (obi-de.app.baqend.com, confidence 55, PASSIVE, MEDIUM-HIGH impact) — PARKED pending platform confirmation
[NEXT] PROBE: GET https://assets.obi.de/seller-side-panel/resources/index-BUGS3Fny.js with Accept: application/javascript and analyze response for hardcoded API endpoints, auth tokens, seller portal URLs, MuleSoft references, and JWT handling logic
[LEARN] REJECTED ENDPOINT-MAP @ www.obi.de: CONFIRMED — /account/api/public/jwt/validate and /explore/recommendations/api/internal/v6/ both return 404 to HEAD/curl; origin routing requires browser UA + cookies per CloudFront edge behavior
[LEARN] ACCEPTED MISCONFIG @ assets.obi.de: Seller onboarding JS bundle (index-BUGS3Fny.js) publicly accessible on S3-backed CDN with CORS: * — no auth gate, enables static analysis for secrets/endpoints
[RISK] obi: 25/100 — All probes passive GET/HEAD at ≤1 rps; no customer data accessed; no auth bypass attempted; next probe fetches public JS bundle from CDN; MuleSoft portal is intentionally public developer portal; program rules prohibit data exposure during testing — risk remains low
[PRIO] api.live.app.obi.de,8.1,attack_surface=8(mobile API v1),business_value=9(customer auth/data),tech_exposure=8(Envoy/versioned),gate_ease=6(needs enum),cloud_surface=7(Envoy),freshness=8(active)
[PRIO] assets.obi.de,7.6,attack_surface=7(S3 CDN + seller JS),business_value=8(seller onboarding),tech_exposure=8(CORS:*+exposed JS),gate_ease=9(public),cloud_surface=8(S3),freshness=7(recent bundle)
[PRIO] imgix.obi.de,6.8,attack_surface=6(image CDN),business_value=6(media),tech_exposure=7(CORS:* S3),gate_ease=10(public),cloud_surface=7(S3),freshness=6(standard)
[PRIO] obi-de.app.baqend.com,6.2,attack_surface=5(BaaS),business_value=7(speed kit data),tech_exposure=5(unknown),gate_ease=5(needs auth),cloud_surface=6(BaaS),freshness=6(active)
[PRIO] www.obi.de,8.5,attack_surface=9(multi-API),business_value=10(e-comm 10M),tech_exposure=8(Vtex/JWT),gate_ease=4(browser UA only),cloud_surface=8(CF+Baqend),freshness=8(Vtex 2024)
[PRIO] api.obi.com,9.2,attack_surface=10(14+ APIs),business_value=9(marketplace),tech_exposure=9(MuleSoft/CORS:*),gate_ease=10(public),cloud_surface=8(CF+MuleSoft),freshness=9(seller portal)
[HYP] Mobile API Versioned Endpoint Enumeration & Auth Bypass
class: AUTH
asset: api.live.app.obi.de
confidence: 70
reasoning: NEW asset, Envoy proxy with /v1/ versioned API path. Mobile app APIs often have weaker auth gates than web. Versioned paths (/v1/, /v2/) suggest legacy endpoints may persist without auth updates. No probes executed yet.
evidence_needed: Confirm /v1/ base path responds; enumerate sub-paths (/v1/auth, /v1/user, /v1/orders); test auth requirements per endpoint
verify_steps: GET https://api.live.app.obi.de/v1/ → GET https://api.live.app.obi.de/v1/health → GET https://api.live.app.obi.de/v1/auth/login → OPTIONS https://api.live.app.obi.de/v1/ (CORS) → test POST /v1/auth/login with empty body
impact: Full customer account access, order history, payment methods, PII for mobile app users. Severity: HIGH.
testability: PASSIVE
[HYP] Seller Onboarding JS Bundle Secrets & Hardcoded Endpoints
class: MISCONFIG
asset: assets.obi.de
confidence: 80
reasoning: NEW finding — seller-side-panel/resources/index-BUGS3Fny.js served from S3-backed CDN with no auth gate. Frontend bundles often contain API keys, internal endpoints, auth token formats, or seller portal URLs. CORS: * on parent domain enables cross-origin read.
evidence_needed: Extract and analyze JS bundle for hardcoded secrets, API endpoints, auth logic, seller portal URLs, token storage patterns
verify_steps: GET https://assets.obi.de/seller-side-panel/resources/index-BUGS3Fny.js → static analysis for endpoints/keys/tokens → check for MuleSoft API URLs, JWT handling, seller dashboard paths → verify any found endpoints respond
impact: Exposure of seller onboarding flow, internal API topology, potential API keys, auth bypass vectors for seller portal. Severity: HIGH.
testability: PASSIVE
[HYP] Baqend BaaS Speed Kit Data Exposure via Misconfigured Permissions
class: IDOR
asset: obi-de.app.baqend.com
confidence: 55
reasoning: NEW asset — Baqend BaaS (Backend-as-a-Service) speed kit integration. BaaS platforms often expose data APIs with object-level permissions. If speed kit caches customer/session data with predictable IDs, cross-user data access possible. No auth requirement confirmed.
evidence_needed: Confirm Baqend app responds; identify data collections (users, sessions, carts); test IDOR on object IDs
verify_steps: GET https://obi-de.app.baqend.com/ → GET https://obi-de.app.baqend.com/api/ → check for /db/ or /api/v1/ paths → test GET /api/v1/users/1, /api/v1/sessions/{id} with incrementing IDs
impact: Customer session data, cart contents, PII leakage across 10M+ users. Severity: MEDIUM-HIGH.
testability: PASSIVE
[PARKED] Baqend BaaS Speed Kit Data Exposure via Misconfigured Permissions: confidence 55 but Baqend platform specifics unknown; verify steps generic; may require auth from start; lower priority vs confirmed exposed assets
[FINAL] 1. Seller Onboarding JS Bundle Secrets & Hardcoded Endpoints (assets.obi.de, confidence 80, PASSIVE, HIGH impact)
[FINAL] 2. Mobile API Versioned Endpoint Enumeration & Auth Bypass (api.live.app.obi.de, confidence 70, PASSIVE, HIGH impact)
[FINAL] 3. Baqend BaaS Speed Kit Data Exposure via Misconfigured Permissions (obi-de.app.baqend.com, confidence 55, PASSIVE, MEDIUM-HIGH impact) — PARKED pending platform confirmation
[NEXT] PROBE: GET https://assets.obi.de/seller-side-panel/resources/index-BUGS3Fny.js with Accept: application/javascript and analyze response for hardcoded API endpoints, auth tokens, seller portal URLs, MuleSoft references, and JWT handling logic
[LEARN] REJECTED ENDPOINT-MAP @ www.obi.de: CONFIRMED — /account/api/public/jwt/validate and /explore/recommendations/api/internal/v6/ both return 404 to HEAD/curl; origin routing requires browser UA + cookies per CloudFront edge behavior
[LEARN] ACCEPTED MISCONFIG @ assets.obi.de: Seller onboarding JS bundle (index-BUGS3Fny.js) publicly accessible on S3-backed CDN with CORS: * — no auth gate, enables static analysis for secrets/endpoints
[RISK] obi: 25/100 — All probes passive GET/HEAD at ≤1 rps; no customer data accessed; no auth bypass attempted; next probe fetches public JS bundle from CDN; MuleSoft portal is intentionally public developer portal; program rules prohibit data exposure during testing — risk remains low
## 2026-09-03 22:28:21 UTC [target] (model nemotron3)
[NEW] api.live.app.obi.de/v1/ → HTTP 200 (base path accessible, Envoy proxy confirmed)
[NEW] api.live.app.obi.de/v1/health → HTTP 401 (endpoint exists, auth required)
[NEW] api.live.app.obi.de/v1/auth/login → HTTP 401 (endpoint exists, auth required)
[CHANGED] assets.obi.de/seller-side-panel/resources/index-BUGS3Fny.js → HTTP 404 (was accessible per knowledge base, now rotated/removed)
[PRIO] api.obi.com,9.2,attack_surface=10(14+ APIs),business_value=9(marketplace),tech_exposure=9(MuleSoft/CORS:*),gate_ease=10(public),cloud_surface=8(CF+MuleSoft),freshness=9(seller portal)
[PRIO] api.live.app.obi.de,8.5,attack_surface=9(mobile API v1 + enum),business_value=9(customer auth/data),tech_exposure=8(Envoy/versioned),gate_ease=7(base path open),cloud_surface=7(Envoy),freshness=9(active)
[PRIO] www.obi.de,8.5,attack_surface=9(multi-API),business_value=10(e-comm 10M),tech_exposure=8(Vtex/JWT),gate_ease=4(browser UA only),cloud_surface=8(CF+Baqend),freshness=8(Vtex 2024)
[PRIO] assets.obi.de,6.5,attack_surface=6(S3 CDN),business_value=7(seller onboarding),tech_exposure=6(CORS:* but JS rotated),gate_ease=8(public),cloud_surface=8(S3),freshness=5(bundle 404)
[PRIO] obi-de.app.baqend.com,6.2,attack_surface=5(BaaS),business_value=7(speed kit data),tech_exposure=5(unknown),gate_ease=5(needs auth),cloud_surface=6(BaaS),freshness=6(active)
[PRIO] imgix.obi.de,6.8,attack_surface=6(image CDN),business_value=6(media),tech_exposure=7(CORS:* S3),gate_ease=10(public),cloud_surface=7(S3),freshness=6(standard)
[HYP] Mobile API v1 Base Path Enumeration & Auth Bypass
class: AUTH
asset: api.live.app.obi.de
confidence: 80
reasoning: /v1/ returns 200 (base path accessible), /v1/health and /v1/auth/login return 401 (endpoints exist, require auth). Mobile APIs often have weaker auth gates; versioned paths suggest legacy endpoints may persist without auth updates. Envoy proxy indicates Kubernetes/microservices backend.
evidence_needed: Enumerate sub-paths under /v1/ (users, orders, cart, profile, products); test auth requirements per endpoint; check for unauthenticated info leakage at /v1/
verify_steps: GET https://api.live.app.obi.de/v1/ → GET https://api.live.app.obi.de/v1/users → GET https://api.live.app.obi.de/v1/orders → GET https://api.live.app.obi.de/v1/cart → GET https://api.live.app.obi.de/v1/profile → OPTIONS https://api.live.app.obi.de/v1/ (CORS) → POST https://api.live.app.obi.de/v1/auth/login with empty body
impact: Full customer account access, order history, payment methods, PII for mobile app users. Severity: HIGH.
testability: PASSIVE
[HYP] MuleSoft API Portal — Unauthenticated API Documentation & Seller Onboarding Exposure
class: MISCONFIG
asset: api.obi.com
confidence: 85
reasoning: Public MuleSoft Exchange portal exposes 14+ marketplace API docs (order, product, price, inventory, transactions, seller) with CORS: *. Portal content fully readable without auth. Seller onboarding JS bundle previously served from assets.obi.de. Login endpoint at /login exists but portal accessible unauthenticated.
evidence_needed: Extract actual endpoint URLs, request/response schemas, auth token formats from API docs; test /login for auth bypass or default creds; verify CORS allows cross-origin reading
verify_steps: GET https://api.obi.com/e9d97593-77e2-4c1d-983c-b4593b3393ed/order-service-management-api/ with Accept: text/html,application/xhtml+xml → follow each API doc link → GET https://api.obi.com/login → OPTIONS https://api.obi.com/ → check for exposed client_id/client_secret in docs
impact: Attackers discover internal marketplace APIs, seller onboarding flows, order/payment/inventory endpoints. Combined with CORS: *, cross-origin JS can read portal. Severity: HIGH.
testability: PASSIVE
[HYP] JWT Validation Endpoint — Potential Algorithm Confusion with Authenticated Session
class: AUTH
asset: www.obi.de/account/api/public/jwt/validate
confidence: 65
reasoning: Frontend JavaScript calls /account/api/public/jwt/validate to check session state. Endpoint returns 404 to HEAD/curl but may respond to POST with JWT body (browser UA + cookies required). heyOBI uses JWT for customer auth across 10M+ users. If validation accepts alg:none or allows key confusion (RS256→HS256), account takeover is possible.
evidence_needed: Confirm endpoint responds to POST with JWT body under browser-like conditions; identify JWT algorithm and key handling; test alg:none and alg confusion
verify_steps: POST https://www.obi.de/account/api/public/jwt/validate with Content-Type: application/json, browser UA, cookies, body: {"token":""} → observe response → craft test JWT with alg:none → POST with test JWT → check if rejected correctly
impact: Account takeover of 10M+ heyOBI users, access to purchase history, payment methods, personal data. Severity: CRITICAL.
testability: AUTH_HELPED
[PARKED] Baqend BaaS Speed Kit Data Exposure via Misconfigured Permissions: confidence 55 but Baqend platform specifics unknown; verify steps generic; may require auth from start; lower priority vs confirmed exposed assets
[FINAL] 1. MuleSoft API Portal — Unauthenticated API Documentation & Seller Onboarding Exposure (api.obi.com, confidence 85, PASSIVE, HIGH impact)
[FINAL] 2. Mobile API v1 Base Path Enumeration & Auth Bypass (api.live.app.obi.de, confidence 80, PASSIVE, HIGH impact)
[FINAL] 3. JWT Validation Endpoint — Potential Algorithm Confusion (www.obi.de/account/api/public/jwt/validate, confidence 65, AUTH_HELPED, CRITICAL impact)
[NEXT] PROBE: GET https://api.live.app.obi.de/v1/users and GET https://api.live.app.obi.de/v1/orders and GET https://api.live.app.obi.de/v1/cart with Accept: application/json to enumerate accessible endpoints under /v1/ and check for unauthenticated data leakage
[LEARN] ACCEPTED AUTH @ api.live.app.obi.de: /v1/ base path returns 200, /v1/health and /v1/auth/login return 401 — mobile API v1 confirmed live with auth-gated endpoints
[LEARN] REJECTED ENDPOINT-MAP @ www.obi.de: CONFIRMED — /account/api/public/jwt/validate and /explore/recommendations/api/internal/v6/ both return 404 to HEAD/curl; origin routing requires browser UA + cookies per CloudFront edge behavior
[LEARN] CHANGED MISCONFIG @ assets.obi.de: Seller onboarding JS bundle (index-BUGS3Fny.js) now returns 404 — previously accessible, likely rotated/removed; need to discover new bundle filename
[RISK] obi: 30/100 — All probes passive GET/HEAD/OPTIONS at ≤1 rps; no customer data accessed; no auth bypass attempted; next probe enumerates /v1/ sub-paths on mobile API; MuleSoft portal is intentionally public developer portal; program rules prohibit data exposure during testing — risk remains low
## 2026-09-04 00:37:23 UTC [target] (model nemotron3)
[PRIO] api.obi.com,9.2,attack_surface=10(14+ APIs),business_value=9(marketplace),tech_exposure=9(MuleSoft/CORS:*),gate_ease=10(public),cloud_surface=8(CF+MuleSoft),freshness=9(seller portal)
[PRIO] api.live.app.obi.de,8.5,attack_surface=9(mobile API v1 + enum),business_value=9(customer auth/data),tech_exposure=8(Envoy/versioned),gate_ease=7(base path open),cloud_surface=7(Envoy),freshness=9(active)
[PRIO] www.obi.de,8.5,attack_surface=9(multi-API),business_value=10(e-comm 10M),tech_exposure=8(Vtex/JWT),gate_ease=4(browser UA only),cloud_surface=8(CF+Baqend),freshness=8(Vtex 2024)
[PRIO] imgix.obi.de,6.8,attack_surface=6(image CDN),business_value=6(media),tech_exposure=7(CORS:* S3),gate_ease=10(public),cloud_surface=7(S3),freshness=6(standard)
[PRIO] assets.obi.de,6.5,attack_surface=6(S3 CDN),business_value=7(seller onboarding),tech_exposure=6(CORS:* but JS rotated),gate_ease=8(public),cloud_surface=8(S3),freshness=5(bundle 404)
[PRIO] obi-de.app.baqend.com,6.2,attack_surface=5(BaaS),business_value=7(speed kit data),tech_exposure=5(unknown),gate_ease=5(needs auth),cloud_surface=6(BaaS),freshness=6(active)
[HYP] MuleSoft API Portal — Unauthenticated API Documentation & Seller Onboarding Exposure
class: MISCONFIG
asset: api.obi.com
confidence: 85
reasoning: Public MuleSoft Exchange portal exposes 14+ marketplace API docs (order, product, price, inventory, transactions, seller) with CORS: *. Portal content fully readable without auth. Seller onboarding JS bundle previously served from assets.obi.de. Login endpoint at /login exists but portal accessible unauthenticated.
evidence_needed: Extract actual endpoint URLs, request/response schemas, auth token formats from API docs; test /login for auth bypass or default creds; verify CORS allows cross-origin reading
verify_steps: GET https://api.obi.com/e9d97593-77e2-4c1d-983c-b4593b3393ed/order-service-management-api/ with Accept: text/html,application/xhtml+xml → follow each API doc link → GET https://api.obi.com/login → OPTIONS https://api.obi.com/ → check for exposed client_id/client_secret in docs
impact: Attackers discover internal marketplace APIs, seller onboarding flows, order/payment/inventory endpoints. Combined with CORS: *, cross-origin JS can read portal. Severity: HIGH.
testability: PASSIVE
[HYP] Mobile API v1 — Auth Bypass via Versioned Endpoint Weakness
class: AUTH
asset: api.live.app.obi.de
confidence: 75
reasoning: /v1/ base path returns 200 (Envoy proxy confirmed), all enumerated sub-paths (/users, /orders, /cart, /profile, /health, /auth/login) return 401. Mobile APIs often have weaker auth gates on legacy/undocumented endpoints. Versioned paths suggest possible unauthenticated debug/admin endpoints or IDOR on object IDs.
evidence_needed: Discover additional /v1/ sub-paths (admin, debug, v2, beta, internal); test for unauthenticated info leakage at /v1/ root response; check for IDOR on predictable object IDs if any endpoint accepts IDs
verify_steps: GET https://api.live.app.obi.de/v1/ (analyze response body for endpoint hints) → OPTIONS https://api.live.app.obi.de/v1/ (CORS) → GET https://api.live.app.obi.de/v1/admin → GET https://api.live.app.obi.de/v1/debug → GET https://api.live.app.obi.de/v1/v2/ → GET https://api.live.app.obi.de/v1/internal/ → POST https://api.live.app.obi.de/v1/auth/login with empty body
impact: Full customer account access, order history, payment methods, PII for mobile app users. Severity: HIGH.
testability: PASSIVE
[HYP] JWT Validation Endpoint — Potential Algorithm Confusion with Authenticated Session
class: AUTH
asset: www.obi.de/account/api/public/jwt/validate
confidence: 65
reasoning: Frontend JavaScript calls /account/api/public/jwt/validate to check session state. Endpoint returns 404 to HEAD/curl but may respond to POST with JWT body (browser UA + cookies required). heyOBI uses JWT for customer auth across 10M+ users. If validation accepts alg:none or allows key confusion (RS256→HS256), account takeover is possible.
evidence_needed: Confirm endpoint responds to POST with JWT body under browser-like conditions; identify JWT algorithm and key handling; test alg:none and alg confusion
verify_steps: POST https://www.obi.de/account/api/public/jwt/validate with Content-Type: application/json, browser UA, cookies, body: {"token":""} → observe response → craft test JWT with alg:none → POST with test JWT → check if rejected correctly
impact: Account takeover of 10M+ heyOBI users, access to purchase history, payment methods, personal data. Severity: CRITICAL.
testability: AUTH_HELPED
[PARKED] Baqend BaaS Speed Kit Data Exposure via Misconfigured Permissions: confidence 55 but Baqend platform specifics unknown; verify steps generic; may require auth from start; lower priority vs confirmed exposed assets
[PARKED] Internal Recommendations API — Potential IDOR via Product IDs: confidence 55, "internal" path may be naming convention; all /api/* return 404 at CloudFront edge for HEAD/curl; cannot confirm without browser session
[FINAL] 1. MuleSoft API Portal — Unauthenticated API Documentation & Seller Onboarding Exposure (api.obi.com, confidence 85, PASSIVE, HIGH impact)
[FINAL] 2. Mobile API v1 — Auth Bypass via Versioned Endpoint Weakness (api.live.app.obi.de, confidence 75, PASSIVE, HIGH impact)
[FINAL] 3. JWT Validation Endpoint — Potential Algorithm Confusion (www.obi.de/account/api/public/jwt/validate, confidence 65, AUTH_HELPED, CRITICAL impact)
[NEXT] PROBE: GET https://api.obi.com/e9d97593-77e2-4c1d-983c-b4593b3393ed/order-service-management-api/ with Accept: text/html,application/xhtml+xml and User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 — extract API documentation content (endpoints, schemas, auth requirements); also GET https://api.obi.com/login and OPTIONS https://api.obi.com/ to map CORS policy and auth flow
[LEARN] ACCEPTED AUTH @ api.live.app.obi.de: /v1/ base path returns 200, /v1/health and /v1/auth/login and /v1/users and /v1/orders and /v1/cart and /v1/profile all return 401 — mobile API v1 confirmed live, fully auth-gated, no unauthenticated leakage
[LEARN] REJECTED ENDPOINT-MAP @ www.obi.de: CONFIRMED — /account/api/public/jwt/validate and /explore/recommendations/api/internal/v6/ both return 404 to HEAD/curl; origin routing requires browser UA + cookies per CloudFront edge behavior
[LEARN] CHANGED MISCONFIG @ assets.obi.de: Seller onboarding JS bundle (index-BUGS3Fny.js) now returns 404 — previously accessible, likely rotated/removed; need to discover new bundle filename
[RISK] obi: 30/100 — All probes passive GET/HEAD/OPTIONS at ≤1 rps; no customer data accessed; no auth bypass attempted; next probe targets public MuleSoft developer portal (intentionally public); program rules prohibit data exposure during testing — risk remains low
## 2026-09-04 05:18:38 UTC [target] (model nemotron3)
