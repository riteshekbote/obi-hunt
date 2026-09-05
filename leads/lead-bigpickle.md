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
## 2026-09-04 00:28:28 UTC [target] (model bigpickle)
## 2026-09-04 05:08:35 UTC [target] (model bigpickle)
## 2026-09-04 09:45:49 UTC [target] (model bigpickle)
[CHANGED] api.obi.com: Portal returns full JSON catalog of 4+ marketplace APIs with S3 signed download URLs, org IDs, contact emails, version info — previously only confirmed as HTTP 200, now confirmed as full unauthenticated API catalog.
[PRIO] api.obi.com,9.5,attack_surface=10(14+ APIs),business_value=9(marketplace/seller/payment),tech_exposure=10(MuleSoft/CORS:*/full JSON metadata),gate_ease=10(public + no auth),cloud_surface=9(CF+MuleSoft+S3),freshness=9(S3 signed URLs with active tokens)
[PRIO] www.obi.de,8.5,attack_surface=9(multi-API),business_value=10(e-commerce/10M users),tech_exposure=8(Vtex/DiscoverCMS/JWT),gate_ease=7(needs browser UA),cloud_surface=8(CF+Baqend),freshness=8(Vtex migration)
[PRIO] api.live.app.obi.de,7.8,attack_surface=7(mobile API),business_value=9(customer data),tech_exposure=7(Envoy/versioned),gate_ease=6(needs enum),cloud_surface=7(Envoy),freshness=7(active)
[CHANGED] api.obi.com: Full JSON API catalog extracted from SPA HTML body — 4 marketplace APIs with complete metadata (names, descriptions, versions, S3 signed download URLs, org IDs, contact emails). Portal serves JSON embedded in HTML, not requiring JS execution.
[CHANGED] api.obi.com: MuleSoft Anypoint org `trx-fulfillmentsellersteering` (ID `e9d97593-77e2-4c1d-983c-b4593b3393ed`), parent org `f970166b-9dae-4e62-976d-cfccd05e93ff`, domain `obi-smart-technologies-gmbh`
[CHANGED] api.obi.com: S3 signed download URLs for API spec files (OAS/RAML/ZIP) point to `exchange2-asset-manager-kprod-eu.s3.eu-central-1.amazonaws.com` with temp AWS credentials embedded in query strings
[PRIO] api.obi.com,9.5,attack_surface=10(4+ marketplace APIs fully cataloged),business_value=9(marketplace/seller/payment),tech_exposure=10(MuleSoft+CORS:*+full JSON+S3 tokens),gate_ease=10(public+no auth+CORS:*),cloud_surface=9(CF+MuleSoft+S3),freshness=9(S3 signed URLs with active temp tokens)
[PRIO] www.obi.de,8.5,attack_surface=9(multi-API),business_value=10(e-commerce/10M users),tech_exposure=8(Vtex/DiscoverCMS/JWT),gate_ease=7(needs browser UA),cloud_surface=8(CF+Baqend),freshness=8(Vtex migration)
[PRIO] api.live.app.obi.de,7.8,attack_surface=7(mobile API),business_value=9(customer data),tech_exposure=7(Envoy/versioned),gate_ease=6(needs enum),cloud_surface=7(Envoy),freshness=7(active)
[HYP] MuleSoft Exchange Portal — Unauthenticated Full API Catalog & S3 Spec File Access via CORS: *
class: MISCONFIG
asset: api.obi.com
confidence: 92
reasoning: Portal root returns full JSON catalog of 4 marketplace APIs (Product Management v1.1.6, Price Management v1.1.5, Inventory Management v1.1.6, Order Invoice Management v1.0.26) with descriptions, contact emails, org IDs, and S3 signed download URLs for OpenAPI/RAML specs. CORS: * allows any cross-origin JavaScript to read full catalog. Login redirects to eu1.anypoint.mulesoft.com but catalog data requires no auth. Org structure reveals internal project naming ("trx-fulfillmentsellersteering").
evidence_needed: Confirm S3 signed URLs serve actual API spec files (download the OAS/RAML files); verify spec files contain actual endpoint URLs, request/response schemas, auth requirements
verify_steps: GET https://api.obi.com/ with curl and extract JSON → extract S3 download URLs from JSON → GET the OAS spec ZIP via signed URL → unzip and analyze for actual API endpoints/schemas
impact: Attacker maps entire OBI marketplace backend: order flows, price management, inventory systems, invoice handling. S3 signed URLs reveal AWS infrastructure. Combined with CORS:*, automated cross-origin scraping of full API catalog possible. Severity: HIGH
testability: PASSIVE
[HYP] JWT Validation Endpoint — Potential Algorithm Confusion
class: AUTH
asset: www.obi.de/account/api/public/jwt/validate
confidence: 65
reasoning: Frontend JS calls /account/api/public/jwt/validate to check session state. Endpoint returns 404 to HEAD/curl but may respond to POST with JWT body. heyOBI uses JWT for customer auth across 10M+ users. If validation accepts alg:none or allows key confusion (RS256→HS256), account takeover is possible.
evidence_needed: Confirm endpoint responds to POST with JWT body; identify JWT algorithm and key handling; test alg:none and alg confusion
verify_steps: POST https://www.obi.de/account/api/public/jwt/validate with Content-Type: application/json and empty body → observe response → craft test JWT with alg:none → POST with test JWT → check if rejected correctly
impact: Account takeover of 10M+ heyOBI users, access to purchase history, payment methods, personal data. Severity: CRITICAL
testability: AUTH_HELPED
[HYP] Internal Recommendations API Potential IDOR via Product/Customer IDs
class: IDOR
asset: www.obi.de/explore/recommendations/api/internal/v6/
confidence: 55
reasoning: Endpoint path contains "internal" suggesting it was not designed for public access. Prudsys recommendation engine at /explore/recommendations/api/internal/v6/ likely accepts product IDs or customer session tokens. If product IDs are sequential/predictable, cross-tenant recommendation data or customer behavior profiles could be leaked.
evidence_needed: Confirm endpoint accepts requests; identify required parameters (product ID, customer ID, session token); test with incrementing IDs
verify_steps: GET https://www.obi.de/explore/recommendations/api/internal/v6/ → GET with ?productId=2191162265821 → test with incrementing IDs → check response for PII or cross-customer data
impact: Leakage of customer browsing/purchase patterns, product affinity data. Severity: MEDIUM
testability: PASSIVE
[PARKED] Internal Recommendations API — Confidence 55. All /api/ paths return 404 at CloudFront for curl/HEAD. "internal" in path may just be naming convention. Park until browser-confirmed.
[NEXT] PROBE: Download the Product Management API OAS/RAML spec from the S3 signed URL to extract actual API endpoint URLs, request/response schemas, and auth requirements. If S3 token expired, try the Order Invoice Management API spec. The JSON catalog already contains multiple signed URLs with different expiry times.
[LEARN] ACCEPTED MISCONFIG @ api.obi.com: Full JSON API catalog of 4 marketplace APIs (Product, Price, Inventory, Order Invoice Management) extractable from portal HTML body without JS execution. CORS: * confirmed. S3 signed download URLs with temp AWS credentials embedded. Org ID `e9d97593-77e2-4c1d-983c-b4593b3393ed`, org name `trx-fulfillmentsellersteering`.
[LEARN] ACCEPTED AUTH @ www.obi.de: JWT validation endpoint path confirmed in production JavaScript — viable test target for alg confusion with authenticated session.
[LEARN] REJECTED ENDPOINT-MAP @ www.obi.de: All /api/* paths return 404 at CloudFront edge — origin routing requires browser-level session/cookies.
[RISK] obi: 40/100 — MuleSoft portal is a public developer portal by design (it's an Exchange portal for API consumers). Extracting catalog data and downloading publicly-hosted API specs is intended functionality. CORS: * is standard for MuleSoft Exchange. The S3 signed URLs are temporary (86400s expiry) and scoped to MuleSoft's shared infrastructure. No customer data accessed, no auth bypass attempted. Risk is slightly elevated due to the breadth of API information exposed (order, payment, inventory) which aids reconnaissance against other targets.
## 2026-09-04 14:04:58 UTC [target] (model bigpickle)
## 2026-09-04 17:46:37 UTC [target] (model bigpickle)
[HYP] Cross-Seller IDOR via Unscoped Object Endpoints on Marketplace APIs
class: IDOR
asset: api.obi.com/trx-api/fulfillmentsellersteering/{transaction|invoice|order-service}-api
confidence: 52
reasoning: OAS specs define unscoped GETs — /v1/transactions/{transactionId}, /v1/orders/{orderId} (example 1100004695-01), /v1/subscriptions/{id}, /v1/prices — that carry no sellers/{sellerId} context. Client-ID Enforcement validates API access only, not object ownership. If the branch-protected unscoped variants proxy to a data layer without seller scoping, any valid seller credential enumerates other sellers+orders+prices+inventory.
evidence_needed: A valid seller clientId/clientSecret; then GET /v1/transactions/{transactionId} for an orderId belonging to a different seller and compare authorization.
verify_steps: GET https://api.obi.com/trx-api/fulfillmentsellersteering/transaction-api/v1/transactions/{transactionId} with basic auth (valid seller creds) → try other-seller transactionId → expect 403 (authorized) vs 200/leak (vuln).
impact: Cross-tenant extraction of order/invoice/price/transaction/inventory PII + financial data. Severity: HIGH (unproven).
testability: AUTH_HELPED
[HYP] JWT Algorithm Confusion on Session Validation Endpoint
class: AUTH
asset: www.obi.de/account/api/public/jwt/validate
confidence: 65
reasoning: Frontend production JS calls this POST path with JWT to check session state (confirmed in prior recon). Encoding arbitrary header algorithm selection in a public-signing context permits HS256/RS256 confusion → account takeover if key handling is weak.
evidence_needed: Confirm POST accepts a JWT body; capture real token; test alg:none and RS256→HS256 with public key.
verify_steps: POST https://www.obi.de/account/api/public/jwt/validate with Content-Type: application/json, empty body → observe 200/400/401; then craft alg:none JWT → observe acceptance.
impact: Account takeover of 10M+ heyOBI users. Severity: CRITICAL.
testability: AUTH_HELPED
[HYP] Internal EKS Microservice Fleet Exposure via Public Docs
class: MISCONFIG
asset: *.dev-eks.stage-eks.prod-eks.dbs.obi.solutions
confidence: 45
reasoning: Public Exchange docs enumerate 15+ internal service DNS names on dev/stage/prod EKS clusters, all resolving to CloudFront. Production variant at 65.8.180.128 also resolves. Correct path+edge behavior (browser UA / cookies / specific path prefix) may expose unauthenticated internal APIs.
evidence_needed: Find the edge-triggering path/UA; confirm any origin returns non-404.
verify_steps: Replay documented POST /v1/shippingInfo/status with browser UA + Origin + full headers on prod-eks; if 400/401 → origin live.
impact: Recon map of whole internal returns/supply-chain platform; unauthenticated internal API access if edge routes. Severity: MEDIUM.
testability: PASSIVE
[NEXT] PROBE: POST https://www.obi.de/account/api/public/jwt/validate with User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) Chrome/126.0.0.0, Content-Type: application/json, empty body — observe 200/400/401 to confirm the JWT endpoint is reachable from edge and accepts unauthenticated validation (passive boundary, no token data).
[RISK] obi: 42/100 — All probes passive GET/HEAD + 3 empty-POST boundary tests to OBI-owned dev webhook hosts; no customer data accessed; no auth bypass; ≤0.5 rps. Exchange portal + DNS names are OBI-published. Documented creds are dead. Slight elevation due to breadth of live endpoint mapping revealed (order/payment/inventory surface) which strengthens future AUTH_HELPED testing, and the public exposure of internal infra naming. No program-rule violations.
## 2026-09-04 19:54:35 UTC [target] (model bigpickle)
[HYP] Cross-Seller IDOR via Unscoped Object Endpoints on Marketplace APIs
class: IDOR
asset: api.obi.com/trx-api/fulfillmentsellersteering/{transaction|order|invoice}-api/v1/
confidence: 55
reasoning: 401 (not 404) confirms 3 live API services gated by HTTP Basic `mule-realm`. OAS specs (prior session) define unscoped GETs — /v1/transactions/{transactionId}, /v1/orders/{orderId}, /v1/invoices/{id} — with no sellers/{sellerId} context. If MuleSoft Client-ID Enforcement validates API access but not object ownership, a valid seller credential may enumerate other sellers' transactions/orders/invoices (order no. example `1100004695-01`).
evidence_needed: A valid seller clientId/clientSecret (via OBI seller onboarding account); then cross-tenant object-ID fetch compare.
verify_steps: With valid creds, GET https://api.obi.com/trx-api/fulfillmentsellersteering/transaction-api/v1/transactions/{transactionId} for a known-other seller id → expect 403 (authorized) vs 200/leak (vuln). Passive variant: continue mapping the 401 realm details and collecting spec files via portal for exact schemas.
impact: Cross-tenant extraction of order/invoice/transaction/payment data across sellers. Severity: HIGH.
testability: AUTH_HELPED
[HYP] JWT Validation Endpoint — Algorithm Confusion / Path Reachable with POST
class: AUTH
asset: www.obi.de/account/api/public/jwt/validate
confidence: 65
reasoning: Production frontend JS references this POST path for session JWT checks; confirmed 404 to curl/HEAD (edge routing requires browser UA+cookies). JWT alg:none or RS256→HS256 confusion could yield auth bypass across 10M+ heyOBI users.
evidence_needed: Browser-authenticated POST returns non-404; capture a real JWT; test alg:none acceptance.
verify_steps: In authenticated browser session, POST https://www.obi.de/account/api/public/jwt/validate with Content-Type: application/json, empty body → observe 200/400/401 → craft alg:none token → observe acceptance.
impact: Account takeover, purchase/payment history exposure. Severity: CRITICAL.
testability: AUTH_HELPED
[HYP] Marketplace MuleSoft Basic Auth Weakness (shared/weak credentials discoverable via portal assets)
class: AUTH
asset: api.obi.com/trx-api/**
confidence: 40
reasoning: All marketplace APIs enforce HTTP Basic `mule-realm`. MuleSoft Basic typically = clientId:clientSecret; if any onboarding bundle or spec embeds default/sandbox creds (seller-side-panel bundle was previously public), reuse could grant API access.
evidence_needed: Re-discover rotated seller onboarding bundle on assets.obi.de; grep for clientId/secret patterns.
verify_steps: GET https://assets.obi.de/seller-side-panel/resources/ → enumerate new bundle hash-name → grep for `clientId|clientSecret|Basic|Authorization` patterns.
impact: Unauthorized marketplace API access → order/invoice/payment data. Severity: HIGH.
testability: PASSIVE
[NEXT] PROBE: GET https://assets.obi.de/seller-side-panel/ and https://assets.obi.de/ with browser UA to re-discover the rotated seller onboarding bundle filename (index-BUGS3Fny.js → 404; new hash unknown) — passive only, then grep for clientId/clientSecret/Basic creds to feed the marketplace Basic-auth hypothesis.
[RISK] obi: 44/100 — All probes passive GET to public portal + auth-gated (401) API paths; ≤1 rps; no credentialed tests, no customer data touched, no mutation. Confirmed 401 boundaries only (matching documented Client-ID Enforcement design). Slight elevation: confirmed live financial-order surface (transaction/order/invoice APIs) strengthens future AUTH_HELPED work and reveals internal Envoy op naming; stale signed URLs not re-generated. No program-rule violations.
## 2026-09-04 22:13:53 UTC [target] (model bigpickle)
[PRIO] www.obi.de/account/api/public/jwt/validate,7.75,tech_exposure
[PRIO] api.obi.com/trx-api/fulfillmentsellersteering-apis,5.95,business_value
[PRIO] www.obi.de/explore/recommendations/api/internal/v6,5.75,surface
[HYP] JWT Validation Endpoint Live — Algorithm Confusion / Session Boundary Probe
class: AUTH
asset: www.obi.de/account/api/public/jwt/validate
confidence: 70
reasoning: Endpoint now confirmed reachable from CloudFront edge with browser UA: GET/HEAD → 200 empty `text/javascript`, clears `obi-auth` cookie via Set-Cookie expire; POST without session → 405 (requires authenticated session). Frontend production JS drives it for session JWT checks. If a real session JWT is validated with weak key handling, HS256/alg:none confusion → ATO.
evidence_needed: Authenticated browser session with obi-auth JWT; POST valid JWT → baseline 2xx/4xx; POST crafted alg:none / RS256→HS256 token → observe acceptance vs rejection.
verify_steps: In authenticated session POST with Content-Type application/json + valid JWT body → expect baseline; then craft alg:none token → expect 401/400 (safe) vs 200 (vuln).
impact: Account takeover across 10M+ heyOBI accounts; purchase/payment data. Severity: CRITICAL.
testability: AUTH_HELPED
[HYP] Cross-Seller IDOR via Unscoped Object Endpoints on Marketplace APIs
class: IDOR
asset: api.obi.com/trx-api/fulfillmentsellersteering/{transaction|order-service|invoice|product|inventory}-api/v1/
confidence: 55
reasoning: 5 service bases live behind HTTP Basic `mule-realm` (confirmed). OAS specs define unscoped GETs — /v1/transactions/{transactionId}, /v1/orders/{orderId} (example 1100004695-01), /v1/invoices/{id}, /v1/products, /v1/prices — with no sellers/{sellerId} context; object fetch returns 401 only, not 404, so paths resolve. Client-ID Enforcement gates API access, likely not object ownership.
evidence_needed: A valid seller clientId/clientSecret; cross-tenant object-ID fetch comparison.
verify_steps: With valid creds GET /v1/transactions/{other-seller-transactionId} → 403 (safe) vs 200/leak (vuln).
impact: Cross-tenant extraction of order/invoice/price/inventory/financial data. Severity: HIGH (unproven).
testability: AUTH_HELPED
[HYP] Internal Recommendations v6 Origin Live Behind Edge — Potential Unauthenticated Query
class: MISCONFIG
asset: www.obi.de/explore/recommendations/api/internal/v6/recommendations
confidence: 45
reasoning: Response changed 404 → 500 with browser UA, proving origin routing + app processing; 500 (not 401/403) suggests missing required params, not auth gate. Broader /api/internal/* family may expose recommendations data or originate SSRF-able category/product params.
evidence_needed: Identify required params to move 500 → 200 (or structure response markers in error body with empty GET vs GET ?productId=).
verify_steps: GET with ?productId=0000000000&categoryId=0 (browser UA) → observe 500→200/400 shift indicating param-driven endpoint.
impact: Inner product/recommendation data or cached category data exfil; low-data but confirms unauthenticated internal queries. Severity: LOW-MEDIUM.
testability: PASSIVE
[NEXT] PROBE: GET https://www.obi.de/account/ and GET https://www.obi.de/account/api/ with browser UA to map the account-api tree and locate sibling public endpoints of the confirmed-live jwt/validate path (same CloudFront behavior family), then compare edge routing vs origin reachability passively.
[LEARN] ACCEPTED AUTH @ www.obi.de: /account/api/public/jwt/validate reachable from edge with browser UA — GET/HEAD 200 (text/javascript, len 0) + clears obi-auth; POST w/o session 405. Corrects prior REJECTED rows (path routes by UA, not truly 404).
[LEARN] CHANGED ENDPOINT-MAP @ www.obi.de: /explore/recommendations/api/internal/v6/recommendations now 500 with browser UA — origin live (was 404); app error not auth gate.
[LEARN] ACCEPTED AUTH @ api.obi.com: 5 marketplace service bases confirmed live 401 Basic realm=mule-realm (transaction, order-service, invoice, product, inventory); price-api/order-api names 404 under that prefix.
[RISK] obi: 42/100 — Probes GET/HEAD/OPTIONS + one empty-POST boundary test to OBI-owned public endpoint, browser UA, <1 rps; no customer data, no real credentials, no auth-bypass attempt (fabricated anonymous cookie returned identical 200). Reachability findings only strengthen AUTH_HELPED planning. No program-rule violations.
## 2026-09-05 00:11:29 UTC [target] (model bigpickle)
[CHANGED] Knowledge base holds 3 superseded REJECTED rows claiming /account/api/public/jwt/validate is 404; the 09-04 ACCEPTED fact (reachable with browser UA, GET/HEAD 200 + clears obi-auth, POST no-session 405) is canonical — curl/HEAD 404 is CloudFront UA-routing, not endpoint absence.
[CHANGED] /explore/recommendations/api/internal/v6/recommendations now 500 (not 401/403) with browser UA — origin app live, error type implies missing-params, not auth gate.
[NEW] None — no additional surface probed since last update; assets.obi.de seller-side-panel fully rotated (404 root + resources), api.live.app.obi.de remains fully 401-gated across all 17 sub-paths.
[PRIO] www.obi.de/account/api/public/jwt/validate,6.65,tech_exposure (a7 b7 t10 g3 c4 f8)
[PRIO] api.obi.com/trx-api/fulfillmentsellersteering-apis,6.35,business_value (a6 b9 t6 g4 c5 f6)
[PRIO] www.obi.de/explore/recommendations/api/internal/v6,5.30,surface (a5 b4 t6 g7 c4 f7)
[HYP] JWT Validation Endpoint — Algorithm Confusion / Session Boundary Probe
class: AUTH
asset: www.obi.de/account/api/public/jwt/validate
confidence: 70
reasoning: Production frontend JS drives this POST endpoint for session-JWT checks; confirmed live from CloudFront with browser UA (GET/HEAD 200 text/javascript len 0, Set-Cookie expires obi-auth; POST w/o session 405). If server trusts header-declared alg permissively, alg:none or RS256→HS256 confusion bypasses validation.
evidence_needed: Authenticated obi-auth JWT; POST valid JWT for baseline, then crafted alg:none / RS256→HS256 token for acceptance-vs-rejection comparison.
verify_steps: Passive now: GET /account/, GET /account/api/, GET /account/api/public/ (browser UA) to enumerate sibling public paths and any token-issue endpoints. Later (auth required): POST JSON with a real session token → 2xx/4xx baseline; POST alg:none artifact → 401/400 (safe) vs 200 (vuln).
impact: Account takeover across 10M+ heyOBI accounts incl. purchase/payment history. Severity: CRITICAL.
testability: AUTH_HELPED
[HYP] Cross-Seller IDOR via Unscoped Object Endpoints on Marketplace APIs
class: IDOR
asset: api.obi.com/trx-api/fulfillmentsellersteering/{transaction|order-service|invoice|product|inventory}-api/v1/
confidence: 55
reasoning: 5 service bases live behind HTTP Basic realm=mule-realm; portal OAS specs declare unscoped GETs /v1/transactions/{transactionId}, /v1/orders/{orderId}, /v1/invoices/{id}, /v1/products — no {sellerId} context in path, and paths return 401 (resolve), not 404. Client-ID Enforcement gates API entry, likely not object ownership.
evidence_needed: Valid seller clientId/clientSecret; cross-tenant object-ID fetch compare.
verify_steps: Passive: pull each of the 4 OAS/RAML spec files via portal S3 signed URLs to map exact parameter schemas + scoping. Later (creds): GET /v1/transactions/{other-seller-id} → 403 (safe) vs 200/leak (vuln).
impact: Cross-tenant extraction of order/invoice/price/inventory/financial data across sellers. Severity: HIGH (unproven).
testability: AUTH_HELPED
[HYP] Internal Recommendations v6 — Unauthenticated Param-Driven Query Leak
class: MISCONFIG
asset: www.obi.de/explore/recommendations/api/internal/v6/recommendations
confidence: 45
reasoning: Response shifted 404 → 500 with browser UA, proving origin app processing; 500 (not 401/403) implies missing/ malformed required params, not authentication. Sibling /api/internal/* family may expose recommendation/category data or accept product/category params.
evidence_needed: Move 500 → 200/400 by supplying required params; observe param-driven behavior.
verify_steps: GET with ?productId=0000000000&categoryId=0 and empty GET (browser UA); compare 500→200/400 shift indicating parameter-driven endpoint; then vary one id for error-body structural markers (never fetch or mutate real customer data).
impact: Inner product/recommendation/category data disclosure; confirms unauthenticated internal query surface. Severity: LOW-MEDIUM.
testability: PASSIVE
[PARKED] Marketplace MuleSoft shared/weak Basic creds via onboarding JS (40): evidence path blocked — seller-side-panel bundle rotated (404 root + resources), re-discovery has diminishing returns; revisit only if new bundle filename surfaces in liveness scans.
[PARKED] Reuse of portal S3 signed download URLs (92 class edge): 86400s expiry temp keys, stale by definition; exploitation is out-of-scope (no customer/financial data exposure); portal CORS:* already banked as ACCEPTED finding — not re-scored.
[FINAL] 1) [70] JWT alg/session-boundary probe @ www.obi.de/account/api/public/jwt/validate 2) [55] Cross-seller IDOR @ api.obi.com/trx-api/**/v1/ 3) [45] Internal v6 param-driven leak @ www.obi.de/explore/recommendations/api/internal/v6.
[NEXT] PROBE: GET https://www.obi.de/account/ and GET https://www.obi.de/account/api/ with User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/126.0.0.0 Safari/537.36, Accept: text/html,application/xhtml+xml — map the account-api tree for sibling public endpoints of the confirmed-live jwt/validate path (same CloudFront UA-routing family), including any token-issue/well-known paths.
[LEARN] ACCEPTED AUTH @ www.obi.de: /account/api/public/jwt/validate reachable from edge with browser UA — GET/HEAD 200 (text/javascript, len 0) + Set-Cookie expires obi-auth; POST w/o session 405. Supersedes three earlier REJECTED rows; curl/HEAD 404 = UA-based edge routing, endpoint is live. (Confirms prior tail.)
[LEARN] ACCEPTED ENDPOINT-MAP @ www.obi.de: /explore/recommendations/api/internal/v6/recommendations returns 500 with browser UA — origin app live; 500 (not 401/403) indicates missing params, candidate for passive parameter fuzzing. (Confirms prior tail.)
[RISK] obi: 43/100 — All actions read-only GET/HEAD/OPTIONS at ≤1 rps with browser UA against OBI-owned endpoints; no credentials, no real customer data, no auth-bypass or mutating tests (JWT/IDOR verifications deferred until an authorized session exists). Reachability/param-behavior observations only; portal CORS:*/S3 findings already banked, no new exposure. Risk reflects confirmed financial-order API surface (transaction/order/invoice) strengthening future AUTH_HELPED work. No program-rule violations.
## 2026-09-05 04:40:21 UTC [target] (model bigpickle)
## 2026-09-05 08:38:40 UTC [target] (model bigpickle)
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
[HYP] JWT Validation Endpoint — Algorithm Confusion / Session Boundary Probe
class: AUTH
asset: www.obi.de/account/api/public/jwt/validate
confidence: 70
reasoning: Endpoint confirmed live (200 text/javascript len 0, clears obi-auth; 406 on json Accept = content-negotiation, not gate; POST unsessioned 405 = app reached, method rejected). Sibling token-issue tree closed → validation is the only live account-api surface; alg confusion (RS256→HS256 alg:none) testable once session JWT exists.
evidence_needed: Authenticated obi-auth JWT; baseline POST valid JWT vs crafted alg:none/HS256 artifact acceptance-vs-rejection.
verify_steps: Passive now: none new (tree exhausted). Later (auth): POST JSON valid JWT → 2xx/4xx baseline; POST alg:none artifact → 401/400 (safe) vs 200 (vuln).
impact: ATO across 10M+ heyOBI accounts incl. payment/purchase history. Severity: CRITICAL.
testability: AUTH_HELPED
[HYP] Cross-Seller IDOR via Unscoped Object Endpoints on Marketplace APIs
class: IDOR
asset: api.obi.com/trx-api/fulfillmentsellersteering/{transaction|order-service|invoice}-api
confidence: 52
reasoning: 5 service bases live behind HTTP Basic realm=mule-realm (401 not 404 = resolvable); portal OAS declares unscoped GETs (/v1/transactions/{id}, /v1/orders/{id}, /v1/invoices/{id}) with no {sellerId} path context; gate is Client-ID Enforcement, likely not object ownership.
evidence_needed: Valid seller clientId/clientSecret for cross-tenant object fetch; spec files (S3 signed) for exact param schemas.
verify_steps: Passive: fetch 4 OAS/RAML specs via any live portal S3 signed URL to confirm scoping fields. Later (creds): GET /v1/transactions/{other-seller-taskId} → 403 (safe) vs 200/leak (vuln).
impact: Cross-tenant order/invoice/financial extraction across sellers. Severity: HIGH (unproven).
testability: AUTH_HELPED
[HYP] Internal Recommendations v6 — Origin Erroring, Not a Query Surface (negative)
class: MISCONFIG
asset: www.obi.de/explore/recommendations/api/internal/v6
confidence: 25
reasoning: All variants (root, /recommendations ± productId/categoryId/0000000000, /health) return identical 500 0-byte with `x-cache: Error from cloudfront` → edge-cached origin exception, response invariant to any input; no 500→400/200 shift observed, so not param-driven and not queryable unauth.
evidence_needed: 500→non-500 shift on any unknown required header/param to justify revisit.
verify_steps: Remaining passive: vary Host/X-Forwarded-Host? No — dead end reached; no further probes.
impact: None observable; origin liveness only. Severity: INFO.
testability: PASSIVE
[RISK] obi: 40/100 — All probes read-only GET/POST-empty-boundary at ≤1 rps with browser UA on OBI-owned public endpoints; jwt/validate POST carried empty/token-less body (no credentials, no auth bypass); no customer/financial data touched; imgix/baqend/recommendations were single-shot liveness checks. Net: refined two live findings (confirms AUTH_HELPED targets) and retired three dead-end assets from active list. No program-rule violations; description-error reports avoided.
## 2026-09-05 12:06:33 UTC [target] (model bigpickle)
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
[HYP] JWT Validation Endpoint — Algorithm Confusion / Session Boundary Probe
class: AUTH
asset: www.obi.de/account/api/public/jwt/validate
confidence: 70
reasoning: Endpoint confirmed live (200 text/javascript len 0, clears obi-auth; 406 on json Accept = content-negotiation, not gate; POST unsessioned 405 = app reached, method rejected). Sibling token-issue tree closed → validation is the only live account-api surface; alg confusion (RS256→HS256 alg:none) testable once session JWT exists.
evidence_needed: Authenticated obi-auth JWT; baseline POST valid JWT vs crafted alg:none/HS256 artifact acceptance-vs-rejection.
verify_steps: Passive now: none new (tree exhausted). Later (auth): POST JSON valid JWT → 2xx/4xx baseline; POST alg:none artifact → 401/400 (safe) vs 200 (vuln).
impact: ATO across 10M+ heyOBI accounts incl. payment/purchase history. Severity: CRITICAL.
testability: AUTH_HELPED
[HYP] Cross-Seller IDOR via Unscoped Object Endpoints on Marketplace APIs
class: IDOR
asset: api.obi.com/trx-api/fulfillmentsellersteering/{transaction|order-service|invoice}-api
confidence: 52
reasoning: 5 service bases live behind HTTP Basic realm=mule-realm (401 not 404 = resolvable); portal OAS declares unscoped GETs (/v1/transactions/{id}, /v1/orders/{id}, /v1/invoices/{id}) with no {sellerId} path context; gate is Client-ID Enforcement, likely not object ownership.
evidence_needed: Valid seller clientId/clientSecret for cross-tenant object fetch; spec files (S3 signed) for exact param schemas.
verify_steps: Passive: fetch 4 OAS/RAML specs via any live portal S3 signed URL to confirm scoping fields. Later (creds): GET /v1/transactions/{other-seller-taskId} → 403 (safe) vs 200/leak (vuln).
impact: Cross-tenant order/invoice/financial extraction across sellers. Severity: HIGH (unproven).
testability: AUTH_HELPED
[HYP] Internal Recommendations v6 — Origin Erroring, Not a Query Surface (negative)
class: MISCONFIG
asset: www.obi.de/explore/recommendations/api/internal/v6
confidence: 25
reasoning: All variants (root, /recommendations ± productId/categoryId/0000000000, /health) return identical 500 0-byte with `x-cache: Error from cloudfront` → edge-cached origin exception, response invariant to any input; no 500→400/200 shift observed, so not param-driven and not queryable unauth.
evidence_needed: 500→non-500 shift on any unknown required header/param to justify revisit.
verify_steps: Remaining passive: vary Host/X-Forwarded-Host? No — dead end reached; no further probes.
impact: None observable; origin liveness only. Severity: INFO.
testability: PASSIVE
[RISK] obi: 40/100 — All probes read-only GET/POST-empty-boundary at ≤1 rps with browser UA on OBI-owned public endpoints; jwt/validate POST carried empty/token-less body (no credentials, no auth bypass); no customer/financial data touched; imgix/baqend/recommendations were single-shot liveness checks. Net: refined two live findings (confirms AUTH_HELPED targets) and retired three dead-end assets from active list. No program-rule violations; description-error reports avoided.
[NEW] api.obi.com/trx-api/fulfillmentsellersteering/seller-data-hub-service/api/v1/public/ — unauth service tree diverging from siblings (feature-toggle HTTP 200, no mule-realm 401), discovered from live seller bundle
[CHANGED] assets.obi.de seller bundle is LIVE at /seller-onboarding/seller-side-panel/resources/index-BUGS3Fny.js → 200 (230KB, application/javascript); prior 404s were wrong path (missing /seller-onboarding/ prefix), bundle never rotated
[NEW] www.obi.de live DOM embeds obi-de.app.baqend.com/v1/speedkit/install.js?d=production + customer-center/regi-auth bundles — Baqend confirmed in client runtime (was parked)
[PRIO] api.obi.com,8.8,attack_surface=9(new public service tree vs gated siblings),business_value=8(seller/marketplace),tech_exposure=8(MuleSoft+feature-flag oracle+CORS *),gate_ease=10(public),cloud_surface=8(CF+MuleSoft+S3),freshness=10(updated bundle serves it today)
[PRIO] www.obi.de,8.4,attack_surface=9(SPA exposes cart/regi/customer-center bundles),business_value=10(10M e-comm),tech_exposure=8(Vtex/JWT),gate_ease=5(browser-UA),cloud_surface=8,freshness=8
[PRIO] assets.obi.de,7.6,attack_surface=7(public S3 CDN + 230KB seller bundle),business_value=8(seller onboarding),tech_exposure=8(CORS *),gate_ease=9(public),cloud_surface=8(S3),freshness=9(bundle confirmed current)
[HYP] Seller Data Hub Unauth /public/ Tree — Feature-Flag Disclosure + Seller-ID Oracle
class: MISCONFIG
asset: api.obi.com/trx-api/fulfillmentsellersteering/seller-data-hub-service/api/v1/public/
confidence: 65
reasoning: Five sibling service bases 401 realm=mule-realm, but this base answers unauthenticated: /public/de/feature-toggle → 200 full JSON of internal Unleash-style flags (SOA.412-isDocumentUploadEnabled, soa.1262-send-mail-implementation, soa.1858-invitation-details-from-postgres-db, project:"default"); /public/de/seller-side-panel/{vtexSellerId} → 404 JSON (\u201cThe trxId not found for vtexSellerId: X and country: DE\u201d) = input-dependent oracle revealing internal mapping (vtexSellerId → trxId), fetched by the live bundle with mode:cors + credentials:include. No auth gate observed on the /public/ prefix.
evidence_needed: (a) full public tree content — does any /public/{cc}/ handler return seller objects, upload tokens, or documented config beyond imprint? (b) whether isDocumentUploadEnabled reveals an unauth upload path; (c) response on a valid vtexSellerId vs random.
verify_steps: GET https://api.obi.com/trx-api/fulfillmentsellersteering/seller-data-hub-service/api/v1/public/de/ → GET .../public/de/feature-toggle (done, 200) → GET .../public/de/seller-side-panel/0000000000000 (synthetic) → OPTIONS .../public/de/feature-toggle (CORS/credentials introspection, passive)
impact: Internal feature-flag / project names leak (recon), vtexSellerId enumeration oracle; if a non-public handler is reachable under /public/ → seller config/PII. Severity: MEDIUM-HIGH (recon), HIGH if tree yields objects.
testability: PASSIVE
[HYP] JWT Validation Endpoint — Algorithm Confusion / Session Boundary Probe
class: AUTH
asset: www.obi.de/account/api/public/jwt/validate
confidence: 70
reasoning: Endpoint live with browser UA (GET/HEAD 200 text/javascript len 0, clears obi-auth; POST unsessioned 405; 406 on json Accept = content-negotiation). Regi/auth bundle referenced in live homepage DOM (/regi/auth/assets/regi-hey-obi-login-de_de-inc.html-*.js) — JWT issue/validate flow is asset-confirmed. Alg confusion (RS256→HS256/none) testable only with a session JWT.
evidence_needed: Authenticated obi-auth JWT; baseline valid vs crafted alg:none POST acceptance/rejection.
verify_steps: Passive: none new. Later (auth): POST JSON valid JWT → baseline; POST alg:none artifact → 401/400 (safe) vs 200 (vuln).
impact: ATO 10M+ heyOBI accounts incl. payment/purchase history. Severity: CRITICAL.
testability: AUTH_HELPED
[HYP] Cross-Seller IDOR via Unscoped Object Endpoints on Marketplace APIs
class: IDOR
asset: api.obi.com/trx-api/fulfillmentsellersteering/{transaction|order-service|invoice|seller-data-hub}-service
confidence: 52
reasoning: 5 service bases live behind Basic realm=mule-realm; portal OAS declares unscoped GETs (/v1/transactions/{id}, /v1/orders/{id}) with no {sellerId} context; now the same prefix shows a public service (seller-data-hub) is reachable without the mule gate, proving the gateway is not uniformly 401 — scoping vs ownership must be validated per object, not per host.
evidence_needed: Valid seller clientId/clientSecret for cross-tenant fetch; OAS/RAML specs confirm no scoping param.
verify_steps: Passive: replay any portal S3 signed spec URL for the new seller-data-hub service; grep OAS for {sellerId} path context. Later (creds): GET /v1/transactions/{other-seller-id} → 403 (safe) vs 200 (vuln).
impact: Cross-tenant order/invoice/financial extraction across sellers. Severity: HIGH (unproven).
testability: AUTH_HELPED
[PARKED] Baqend BaaS Speed Kit Data Exposure: DOM-confirmed (install.js loaded, obi-de.app.baqend.com) but data class still unknown and private; confidence remains 55 — no new payload path; park.
[PARKED] Internal Recommendations v6 500-variant: negative result invariant to input; dead end (confidence 25).
[FINAL] 1. Seller Data Hub Unauth /public/ Tree (api.obi.com, 65, PASSIVE, MEDIUM-HIGH) — new confirmed unauth surface this cycle
[FINAL] 2. JWT Validation alg-confusion (www.obi.de, 70, AUTH_HELPED, CRITICAL) — unchanged, awaiting session JWT
[FINAL] 3. Cross-Seller IDOR (api.obi.com, 52, AUTH_HELPED, HIGH) — extended to seller-data-hub service
[NEXT] PROBE: OPTIONS https://api.obi.com/trx-api/fulfillmentsellersteering/seller-data-hub-service/api/v1/public/de/feature-toggle to map CORS/credentials, then GET https://api.obi.com/trx-api/fulfillmentsellersteering/seller-data-hub-service/api/v1/public/de/ and GET .../public/de/seller-side-panel/0000000000000 to test seller-id oracle resolution and enumerate the public tree for non-imprint handlers (≤1 rps, GET/OPTIONS only)
[LEARN] ACCEPTED MISCONFIG @ api.obi.com: seller-data-hub-service/api/v1/public/ answers unauthenticated — /public/de/feature-toggle 200 (internal SOA.* flags + project names) and /public/de/seller-side-panel/{vtexSellerId} is an input-dependent 404 oracle; siblings are 401 mule-realm, so this is a real gate divergence
[LEARN] ACCEPTED ENDPOINT-MAP @ assets.obi.de: correct bundle path is /seller-onboarding/seller-side-panel/resources/index-BUGS3Fny.js → 200 (230KB); all prior 404s used the bare /seller-side-panel/ path — bundle never rotated, supersedes 09-03/09-04 rotation rows
[LEARN] ACCEPTED AUTH @ api.obi.com: seller bundle fetches seller-data-hub /public/ with mode:cors + credentials:include from assets.obi.de origin; base api.obi.com is CORS:* — cross-origin session-bearing calls are part of the portal design (no validated flaw yet)
[RISK] obi: 30/100 — 16 GETs + 1 OPTIONS at ≤1 rps, browser UA, read-only, against a service base the app itself calls unauthenticated; returned only internal feature-flag names and 400/404 error strings (no customer/financial/auth data); seller bundle is a public CDN asset. No program-rule violations; the seller-data-hub surface is OBI-public by app design.
