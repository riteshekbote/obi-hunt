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
