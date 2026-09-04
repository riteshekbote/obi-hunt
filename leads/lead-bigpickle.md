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
