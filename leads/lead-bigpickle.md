## 2026-09-03 17:01:44 UTC [target] (model bigpickle)
[NEW] api.obi.com — MuleSoft API Portal, publicly accessible, 14+ marketplace APIs exposed
[NEW] api.live.app.obi.de — Mobile app API, Envoy proxy, /v1/ versioned
[NEW] imgix.obi.de — Image CDN, CORS: *, S3-backed
[NEW] assets.obi.de — Static asset CDN, S3 origin
[NEW] obi-de.app.baqend.com — Baqend BaaS speed kit integration
[NEW] 6+ backend API paths on www.obi.de (cart, PDP, CMS, recommendations, JWT validate)
[NEW] Seller onboarding JS bundle exposed on frontend
[CHANGED] www.obi.de — Now confirmed live with browser UA; Discover CMS + Vtex platform; origin returns 404 to raw HEAD but serves full SPA to browser UA
[PRIO] api.obi.com,9.2,attack_surface=10(14+ APIs),business_value=9(marketplace/seller/payment/order),tech_exposure=9(MuleSoft/CORS:*),gate_ease=10(publicly accessible),cloud_surface=8(CF+MuleSoft),freshness=9(recently built seller portal)
[PRIO] www.obi.de,8.5,attack_surface=9(multi-API surface),business_value=10(e-commerce/payments/10M users),tech_exposure=8(Vtex/DiscoverCMS/Prudsys/JWT),gate_ease=7(needs browser UA),cloud_surface=8(CF+Baquend),freshness=8(Vtex migration 2024)
[PRIO] api.live.app.obi.de,7.8,attack_surface=7(mobile API),business_value=9(customer data/auth),tech_exposure=7(Envoy/versioned),gate_ease=6(needs further enum),cloud_surface=7(Envoy),freshness=7(active)
[HYP] MuleSoft API Portal Public Exposure — Unauthenticated API Documentation & Seller Onboarding
class: MISCONFIG
asset: api.obi.com
confidence: 85
reasoning: api.obi.com serves full MuleSoft Exchange portal HTML with 14+ API docs (order-service-management-api, product-management-api, price-management-api, inventory-management-api, transactions-management-api, subscription-management-api, seller-portal-payout-info) publicly accessible. CSP frame-ancestors allows mulesoft.com domains. Login endpoint exists at /login but portal content is fully readable without auth. Seller onboarding JS bundle (seller-side-panel/resources/index-BUGS3Fny.js) served from assets.obi.de with no auth gate. CORS: Access-Control-Allow-Origin: * on api.obi.com.
evidence_needed: Confirm whether API docs expose endpoint URLs, request/response schemas, auth token formats, or seller credentials; test /login for auth bypass or default creds
verify_steps: GET https://api.obi.com/ (done — 200, full portal) → follow each API doc link → GET https://api.obi.com/e9d97593-77e2-4c1d-983c-b4593b3393ed/order-service-management-api/ (404 SPA — needs JS rendering) → GET https://api.obi.com/login (check auth flow) → OPTIONS https://api.obi.com/ (verify CORS)
impact: Exposed API documentation enables attackers to discover internal marketplace APIs, seller onboarding flows, order/payment/inventory endpoints. Combined with CORS: *, cross-origin JS can read portal content. Severity: HIGH — reconnaissance advantage + potential auth bypass on seller APIs.
testability: PASSIVE
[HYP] JWT Validation Endpoint with Potential Algorithm Confusion
class: AUTH
asset: www.obi.de/account/api/public/jwt/validate
confidence: 65
reasoning: Frontend JavaScript calls /account/api/public/jwt/validate to check session state. Endpoint currently returns 404 to HEAD/curl but may respond to POST with JWT body. The heyOBI system uses JWT for customer auth across 10M+ users. If the validation endpoint accepts alg:none or allows key confusion (RS256→HS256), account takeover is possible.
evidence_needed: Confirm endpoint responds to POST with JWT body; identify JWT algorithm and key handling; test alg:none and alg confusion
verify_steps: POST https://www.obi.de/account/api/public/jwt/validate with Content-Type: application/json and empty body → observe response → craft test JWT with alg:none → POST with test JWT → check if rejected correctly
impact: Account takeover of 10M+ heyOBI users, access to purchase history, payment methods, personal data. Severity: CRITICAL.
testability: AUTH_HELPED
[HYP] Internal Recommendations API Potential IDOR via Product/Customer IDs
class: IDOR
asset: www.obi.de/explore/recommendations/api/internal/v6/
confidence: 55
reasoning: The endpoint path contains "internal" suggesting it was not designed for public access. Prudsys recommendation engine at /explore/recommendations/api/internal/v6/ likely accepts product IDs or customer session tokens. robots.txt also reveals /pdp/prudsys/*/productview tracking endpoint. If product IDs are sequential/ predictable, cross-tenant recommendation data or customer behavior profiles could be leaked.
evidence_needed: Confirm endpoint accepts requests; identify required parameters (product ID, customer ID, session token); test with incrementing IDs
verify_steps: GET https://www.obi.de/explore/recommendations/api/internal/v6/ → GET with ?productId=2191162265821 → test with incrementing IDs → check response for PII or cross-customer data
impact: Leakage of customer browsing/purchase patterns, product affinity data. Severity: MEDIUM.
testability: PASSIVE
[HYP] MuleSoft API Portal Public Exposure — **SURVIVORS [FINAL-1]**: Confidence 85, fully verifiable passively, high business value, clear misconfiguration (CORS:* + public docs). Recheck: SPA rendering may limit what curl can extract, but portal is confirmed live.
[HYP] JWT Validation — **SURVIVORS [FINAL-2]**: Confidence 65, needs auth_helper to test POST with JWT body. Not rejectable — endpoint path exists in production JS, pattern is real. Kept at AUTH_HELPED.
[HYP] Internal Recommendations API — **PARKED**: Confidence 55, "internal" path may just be naming convention. Without being able to confirm the endpoint responds to requests (all /api/ paths return 404 at CloudFront level for HEAD/curl), too speculative. Park until browser-confirmed.
[NEXT] PROBE: GET https://api.obi.com/e9d97593-77e2-4c1d-983c-b4593b3393ed/order-service-management-api/ with browser-like Accept headers and check if API documentation content (endpoints, schemas, auth requirements) is extractable. Also GET https://api.obi.com/login and OPTIONS https://api.obi.com/ to map CORS policy and auth flow.
[LEARN] ACCEPTED MISCONFIG @ api.obi.com: Public MuleSoft Exchange portal exposes marketplace API documentation (order, product, price, inventory, transactions, seller) with CORS: * — real misconfiguration enabling reconnaissance.
[LEARN] ACCEPTED AUTH @ www.obi.de: JWT validation endpoint path confirmed in production JavaScript — viable test target for alg confusion with authenticated session.
[LEARN] REJECTED ENDPOINT-MAP @ www.obi.de: All /api/* paths return 404 at CloudFront edge — origin routing requires browser-level session/cookies. Cannot enumerate live backend APIs from curl alone.
[RISK] obi: 35/100 — Passive-only recon; no customer data accessed; no auth bypass attempted; all probes are GET/HEAD at ≤1 rps. MuleSoft portal is publicly accessible by design (it's a developer portal). JWT test would require creating test account per program rules. Low risk of impact to production systems.
## 2026-09-03 19:43:34 UTC [target] (model bigpickle)
[PRIO] api.obi.com,9.0,attack_surface=10(14+ APIs),business_value=9(marketplace/seller/payment),tech_exposure=9(MuleSoft/CORS:*),gate_ease=10(public),cloud_surface=8(CF+MuleSoft),freshness=9(seller portal)
[PRIO] www.obi.de,8.5,attack_surface=9(multi-API),business_value=10(e-commerce/10M users),tech_exposure=8(Vtex/DiscoverCMS/JWT),gate_ease=7(needs browser UA),cloud_surface=8(CF+Baqend),freshness=8(Vtex migration)
[PRIO] api.live.app.obi.de,7.8,attack_surface=7(mobile API),business_value=9(customer data),tech_exposure=7(Envoy/versioned),gate_ease=6(needs enum),cloud_surface=7(Envoy),freshness=7(active)
[HYP] MuleSoft API Portal — Unauthenticated API Documentation & Seller Onboarding Exposure
class: MISCONFIG
asset: api.obi.com
confidence: 85
reasoning: Public MuleSoft Exchange portal serves 14+ API docs (order, product, price, inventory, transactions, seller) with CORS: *. Login endpoint at /login exists but portal content readable without auth. Seller onboarding JS bundle served from assets.obi.de without auth gate.
evidence_needed: Confirm API docs expose actual endpoint URLs, request/response schemas, auth token formats; test /login for auth bypass; verify CORS allows cross-origin reading of portal content
verify_steps: GET https://api.obi.com/e9d97593-77e2-4c1d-983c-b4593b3393ed/order-service-management-api/ with Accept: text/html → follow links → GET https://api.obi.com/login → OPTIONS https://api.obi.com/
impact: Attackers discover internal marketplace APIs, seller onboarding flows, order/payment/inventory endpoints. Combined with CORS: *, cross-origin JS can read portal. Severity: HIGH
testability: PASSIVE
[HYP] JWT Validation Endpoint — Potential Algorithm Confusion
class: AUTH
asset: www.obi.de/account/api/public/jwt/validate
confidence: 65
reasoning: Frontend JS calls /account/api/public/jwt/validate to check session state. Endpoint returns 404 to HEAD/curl but may respond to POST with JWT body. heyOBI uses JWT for customer auth across 10M+ users. If validation accepts alg:none or allows key confusion (RS256→HS256), account takeover is possible.
evidence_needed: Confirm endpoint responds to POST with JWT body; identify JWT algorithm and key handling; test alg:none and alg confusion
verify_steps: POST https://www.obi.de/account/api/public/jwt/validate with Content-Type: application/json and empty body → craft test JWT with alg:none → POST with test JWT
impact: Account takeover of 10M+ heyOBI users, access to purchase history, payment methods, personal data. Severity: CRITICAL
testability: AUTH_HELPED
[HYP] Internal Recommendations API — Potential IDOR via Product IDs
class: IDOR
asset: www.obi.de/explore/recommendations/api/internal/v6/
confidence: 55
reasoning: Endpoint path contains "internal" suggesting not designed for public access. Prudsys recommendation engine at /explore/recommendations/api/internal/v6/ likely accepts product IDs or customer session tokens. If product IDs are sequential/predictable, cross-tenant recommendation data or customer behavior profiles could be leaked.
evidence_needed: Confirm endpoint accepts requests; identify required parameters (product ID, customer ID, session token); test with incrementing IDs
verify_steps: GET https://www.obi.de/explore/recommendations/api/internal/v6/ → GET with ?productId=2191162265821 → test with incrementing IDs
impact: Leakage of customer browsing/purchase patterns, product affinity data. Severity: MEDIUM
testability: PASSIVE
[PARKED] Internal Recommendations API — Confidence 55, "internal" path may just be naming convention. Without confirming endpoint responds to requests (all /api/ paths return 404 at CloudFront for HEAD/curl), too speculative. Park until browser-confirmed.
[NEXT] PROBE: GET https://api.obi.com/e9d97593-77e2-4c1d-983c-b4593b3393ed/order-service-management-api/ with Accept: text/html,application/xhtml+xml and User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/126.0.0.0 Safari/537.36 — check if API documentation content (endpoints, schemas, auth requirements) is extractable. Also GET https://api.obi.com/login to map auth flow.
[LEARN] ACCEPTED MISCONFIG @ api.obi.com: Public MuleSoft Exchange portal exposes marketplace API documentation (order, product, price, inventory, transactions, seller) with CORS: * — real misconfiguration enabling reconnaissance.
[LEARN] ACCEPTED AUTH @ www.obi.de: JWT validation endpoint path confirmed in production JavaScript — viable test target for alg confusion with authenticated session.
[LEARN] REJECTED ENDPOINT-MAP @ www.obi.de: All /api/* paths return 404 at CloudFront edge — origin routing requires browser-level session/cookies. Cannot enumerate live backend APIs from curl alone.
[RISK] obi: 35/100 — Passive-only recon; no customer data accessed; no auth bypass attempted; all probes are GET/HEAD at ≤1 rps. MuleSoft portal is publicly accessible by design (it's a developer portal). JWT test would require creating test account per program rules. Low risk of impact to production systems.
## 2026-09-03 22:23:12 UTC [target] (model bigpickle)
