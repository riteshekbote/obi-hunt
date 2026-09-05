# Hypotheses (ranked)

## RANKED HYPOTHESES 2026-09-02 21:42:23 UTC

## RANKED HYPOTHESES 2026-09-02 23:55:00 UTC

## RANKED HYPOTHESES 2026-09-03 03:49:23 UTC

## RANKED HYPOTHESES 2026-09-03 08:20:36 UTC

## RANKED HYPOTHESES 2026-09-03 12:54:33 UTC

## RANKED HYPOTHESES 2026-09-03 17:18:16 UTC
- [85] api.obi.com: MuleSoft API Portal Public Exposure — Unauthenticated API Documentation & Seller Onboarding (from art/lead_bigpickle.txt)
- NEXT(hypotheses-bigpickle.txt): PROBE: GET https://api.obi.com/e9d97593-77e2-4c1d-983c-b4593b3393ed/order-service-management-api/ with browser-like Accept headers and check if API documentatio
- LEARN: ACCEPTED MISCONFIG @ api.obi.com: Public MuleSoft Exchange portal exposes marketplace API documentation (order, product, price, inventory, transactions, seller)
- LEARN: ACCEPTED AUTH @ www.obi.de: JWT validation endpoint path confirmed in production JavaScript — viable test target for alg confusion with authenticated session.
- LEARN: REJECTED ENDPOINT-MAP @ www.obi.de: All /api/* paths return 404 at CloudFront edge — origin routing requires browser-level session/cookies. Cannot enumerate liv

## RANKED HYPOTHESES 2026-09-03 20:01:48 UTC
- [85] api.obi.com: MuleSoft API Portal — Unauthenticated API Documentation & Seller Onboarding Exposure (from art/lead_bigpickle.txt)
- [70] api.live.app.obi.de: Mobile API Versioned Endpoint Enumeration & Auth Bypass (from art/lead_nemotron3.txt)
- NEXT(hypotheses-nemotron3.txt): PROBE: GET https://assets.obi.de/seller-side-panel/resources/index-BUGS3Fny.js with Accept: application/javascript and analyze response for hardcoded API endpoi
- NEXT(hypotheses-bigpickle.txt): PROBE: GET https://api.obi.com/e9d97593-77e2-4c1d-983c-b4593b3393ed/order-service-management-api/ with Accept: text/html,application/xhtml+xml and User-Agent: M
- LEARN: REJECTED ENDPOINT-MAP @ www.obi.de: CONFIRMED — /account/api/public/jwt/validate and /explore/recommendations/api/internal/v6/ both return 404 to HEAD/curl; ori
- LEARN: ACCEPTED MISCONFIG @ assets.obi.de: Seller onboarding JS bundle (index-BUGS3Fny.js) publicly accessible on S3-backed CDN with CORS: * — no auth gate, enables st
- LEARN: REJECTED ENDPOINT-MAP @ www.obi.de: CONFIRMED — /account/api/public/jwt/validate and /explore/recommendations/api/internal/v6/ both return 404 to HEAD/curl; ori
- LEARN: ACCEPTED MISCONFIG @ assets.obi.de: Seller onboarding JS bundle (index-BUGS3Fny.js) publicly accessible on S3-backed CDN with CORS: * — no auth gate, enables st
- LEARN: ACCEPTED MISCONFIG @ api.obi.com: Public MuleSoft Exchange portal exposes marketplace API documentation (order, product, price, inventory, transactions, seller)
- LEARN: ACCEPTED AUTH @ www.obi.de: JWT validation endpoint path confirmed in production JavaScript — viable test target for alg confusion with authenticated session.
- LEARN: REJECTED ENDPOINT-MAP @ www.obi.de: All /api/* paths return 404 at CloudFront edge — origin routing requires browser-level session/cookies. Cannot enumerate liv

## RANKED HYPOTHESES 2026-09-03 22:28:31 UTC
- [80] api.live.app.obi.de: Mobile API v1 Base Path Enumeration & Auth Bypass (from art/lead_nemotron3.txt)
- NEXT(hypotheses-nemotron3.txt): PROBE: GET https://api.live.app.obi.de/v1/users and GET https://api.live.app.obi.de/v1/orders and GET https://api.live.app.obi.de/v1/cart with Accept: applicati
- LEARN: ACCEPTED AUTH @ api.live.app.obi.de: /v1/ base path returns 200, /v1/health and /v1/auth/login return 401 — mobile API v1 confirmed live with auth-gated endpoin
- LEARN: REJECTED ENDPOINT-MAP @ www.obi.de: CONFIRMED — /account/api/public/jwt/validate and /explore/recommendations/api/internal/v6/ both return 404 to HEAD/curl; ori
- LEARN: CHANGED MISCONFIG @ assets.obi.de: Seller onboarding JS bundle (index-BUGS3Fny.js) now returns 404 — previously accessible, likely rotated/removed; need to disc

## RANKED HYPOTHESES 2026-09-04 00:37:33 UTC
- [85] api.obi.com: MuleSoft API Portal — Unauthenticated API Documentation & Seller Onboarding Exposure (from art/lead_nemotron3.txt)
- NEXT(hypotheses-nemotron3.txt): PROBE: GET https://api.obi.com/e9d97593-77e2-4c1d-983c-b4593b3393ed/order-service-management-api/ with Accept: text/html,application/xhtml+xml and User-Agent: M
- LEARN: ACCEPTED AUTH @ api.live.app.obi.de: /v1/ base path returns 200, /v1/health and /v1/auth/login and /v1/users and /v1/orders and /v1/cart and /v1/profile all ret
- LEARN: REJECTED ENDPOINT-MAP @ www.obi.de: CONFIRMED — /account/api/public/jwt/validate and /explore/recommendations/api/internal/v6/ both return 404 to HEAD/curl; ori
- LEARN: CHANGED MISCONFIG @ assets.obi.de: Seller onboarding JS bundle (index-BUGS3Fny.js) now returns 404 — previously accessible, likely rotated/removed; need to disc

## RANKED HYPOTHESES 2026-09-04 05:18:47 UTC

## RANKED HYPOTHESES 2026-09-04 09:49:39 UTC
- [92] api.obi.com: MuleSoft Exchange Portal — Unauthenticated Full API Catalog & S3 Spec File Access via CORS: * (from art/lead_bigpickle.txt)
- [85] api.obi.com: MuleSoft API Portal — Unauthenticated API Documentation & Seller Onboarding Exposure (from art/lead_nemotron3.txt)
- NEXT(hypotheses-nemotron3.txt): PROBE: GET https://api.obi.com/e9d97593-77e2-4c1d-983c-b4593b3393ed/order-service-management-api/ with Accept: text/html,application/xhtml+xml and User-Agent: M
- NEXT(hypotheses-bigpickle.txt): PROBE: Download the Product Management API OAS/RAML spec from the S3 signed URL to extract actual API endpoint URLs, request/response schemas, and auth requirem
- LEARN: ACCEPTED MISCONFIG @ api.obi.com: Public MuleSoft Exchange portal exposes marketplace API documentation (order, product, price, inventory, transactions, seller)
- LEARN: ACCEPTED AUTH @ api.live.app.obi.de: /v1/ base path returns 200, /v1/health and /v1/auth/login and /v1/users and /v1/orders and /v1/cart and /v1/profile and /v1
- LEARN: REJECTED ENDPOINT-MAP @ www.obi.de: CONFIRMED — /account/api/public/jwt/validate and /explore/recommendations/api/internal/v6/ both return 404 to HEAD/curl; ori
- LEARN: CHANGED MISCONFIG @ assets.obi.de: Seller onboarding JS bundle (index-BUGS3Fny.js) now returns 404 — previously accessible, likely rotated/removed; need to disc
- LEARN: ACCEPTED MISCONFIG @ api.obi.com: Full JSON API catalog of 4 marketplace APIs (Product, Price, Inventory, Order Invoice Management) extractable from portal HTML
- LEARN: ACCEPTED AUTH @ www.obi.de: JWT validation endpoint path confirmed in production JavaScript — viable test target for alg confusion with authenticated session.
- LEARN: REJECTED ENDPOINT-MAP @ www.obi.de: All /api/* paths return 404 at CloudFront edge — origin routing requires browser-level session/cookies.

## RANKED HYPOTHESES 2026-09-04 14:16:57 UTC
- [92] api.obi.com: MuleSoft Exchange Portal — Unauthenticated Full API Catalog & S3 Spec File Access via CORS: * (from art/lead_nemotron3.txt)
- NEXT(hypotheses-nemotron3.txt): PROBE: GET https://api.obi.com/ with Accept: text/html,application/xhtml+xml and User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 — extr
- LEARN: ACCEPTED MISCONFIG @ api.obi.com: Full JSON API catalog of 4 marketplace APIs (Product, Price, Inventory, Order Invoice Management) extractable from portal HTML
- LEARN: ACCEPTED AUTH @ api.live.app.obi.de: /v1/ base path returns 200, all 10 tested sub-paths (/users, /orders, /cart, /profile, /health, /auth/login, /admin, /debug
- LEARN: REJECTED ENDPOINT-MAP @ www.obi.de: CONFIRMED — /account/api/public/jwt/validate and /explore/recommendations/api/internal/v6/ both return 404 to HEAD/curl; ori
- LEARN: CHANGED MISCONFIG @ assets.obi.de: Seller onboarding JS bundle (index-BUGS3Fny.js) now returns 404 — previously accessible, likely rotated/removed; need to disc
- LEARN: ACCEPTED AUTH @ www.obi.de: JWT validation endpoint path confirmed in production JavaScript — viable test target for alg confusion with authenticated session.
- LEARN: REJECTED ENDPOINT-MAP @ www.obi.de: All /api/* paths return 404 at CloudFront edge — origin routing requires browser-level session/cookies.

## RANKED HYPOTHESES 2026-09-04 17:50:25 UTC
- [92] api.obi.com: MuleSoft Exchange Portal — Unauthenticated Full API Catalog & S3 Spec File Access via CORS: * (from art/lead_nemotron3.txt)
- [52] api.obi.com/trx-api/fulfillmentsellersteering/{transaction|invoice|order-service}-api: Cross-Seller IDOR via Unscoped Object Endpoints on Marketplace APIs (from art/lead_bigpickle.txt)
- NEXT(hypotheses-bigpickle.txt): PROBE: POST https://www.obi.de/account/api/public/jwt/validate with User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) Chrome/126.0.0.0, Content-Type: applic
- NEXT(hypotheses-nemotron3.txt): PROBE: GET https://api.obi.com/ with Accept: text/html,application/xhtml+xml and User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 — extr
- LEARN: ACCEPTED MISCONFIG @ api.obi.com: Full JSON API catalog of 4 marketplace APIs (Product, Price, Inventory, Order Invoice Management) extractable from portal HTML
- LEARN: ACCEPTED AUTH @ api.live.app.obi.de: /v1/ base path returns 200, all 17 tested sub-paths (/users, /orders, /cart, /profile, /health, /auth/login, /admin, /debug
- LEARN: REJECTED ENDPOINT-MAP @ www.obi.de: CONFIRMED — /account/api/public/jwt/validate and /explore/recommendations/api/internal/v6/ both return 404 to HEAD/curl; ori
- LEARN: CHANGED MISCONFIG @ assets.obi.de: Seller onboarding JS bundle (index-BUGS3Fny.js) now returns 404 — previously accessible, likely rotated/removed; need to disc
- LEARN: ACCEPTED AUTH @ www.obi.de: JWT validation endpoint path confirmed in production JavaScript — viable test target for alg confusion with authenticated session.
- LEARN: REJECTED ENDPOINT-MAP @ www.obi.de: All /api/* paths return 404 at CloudFront edge — origin routing requires browser-level session/cookies.

## RANKED HYPOTHESES 2026-09-04 20:13:00 UTC
- [55] api.obi.com/trx-api/fulfillmentsellersteering/{transaction|order|invoice}-api/v1/: Cross-Seller IDOR via Unscoped Object Endpoints on Marketplace APIs (from art/lead_bigpickle.txt)
- NEXT(hypotheses-bigpickle.txt): PROBE: GET https://assets.obi.de/seller-side-panel/ and https://assets.obi.de/ with browser UA to re-discover the rotated seller onboarding bundle filename (ind

## RANKED HYPOTHESES 2026-09-04 22:15:24 UTC
- [92] api.obi.com: MuleSoft Exchange Portal — Unauthenticated Full API Catalog & S3 Spec File Access via CORS: * (from art/lead_nemotron3.txt)
- [70] www.obi.de/account/api/public/jwt/validate: JWT Validation Endpoint Live — Algorithm Confusion / Session Boundary Probe (from art/lead_bigpickle.txt)
- NEXT(hypotheses-bigpickle.txt): PROBE: GET https://www.obi.de/account/ and GET https://www.obi.de/account/api/ with browser UA to map the account-api tree and locate sibling public endpoints o
- NEXT(hypotheses-nemotron3.txt): PROBE: GET https://api.obi.com/ with Accept: text/html,application/xhtml+xml and User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 — extr
- LEARN: ACCEPTED AUTH @ www.obi.de: /account/api/public/jwt/validate reachable from edge with browser UA — GET/HEAD 200 (text/javascript, len 0) + clears obi-auth; POST
- LEARN: CHANGED ENDPOINT-MAP @ www.obi.de: /explore/recommendations/api/internal/v6/recommendations now 500 with browser UA — origin live (was 404); app error not auth 
- LEARN: ACCEPTED AUTH @ api.obi.com: 5 marketplace service bases confirmed live 401 Basic realm=mule-realm (transaction, order-service, invoice, product, inventory); pr
- LEARN: ACCEPTED MISCONFIG @ api.obi.com: Full JSON API catalog of 4 marketplace APIs (Product, Price, Inventory, Order Invoice Management) extractable from portal HTML
- LEARN: ACCEPTED AUTH @ api.live.app.obi.de: /v1/ base path returns 200, all 17 tested sub-paths (/users, /orders, /cart, /profile, /health, /auth/login, /admin, /debug
- LEARN: REJECTED ENDPOINT-MAP @ www.obi.de: CONFIRMED — /account/api/public/jwt/validate and /explore/recommendations/api/internal/v6/ both return 404 to HEAD/curl; ori
- LEARN: CHANGED MISCONFIG @ assets.obi.de: Seller onboarding JS bundle (index-BUGS3Fny.js) now returns 404 — previously accessible, likely rotated/removed; need to disc
- LEARN: ACCEPTED AUTH @ www.obi.de: JWT validation endpoint path confirmed in production JavaScript — viable test target for alg confusion with authenticated session.
- LEARN: REJECTED ENDPOINT-MAP @ www.obi.de: All /api/* paths return 404 at CloudFront edge — origin routing requires browser-level session/cookies.

## RANKED HYPOTHESES 2026-09-05 00:15:43 UTC
- [70] www.obi.de/account/api/public/jwt/validate: JWT Validation Endpoint — Algorithm Confusion / Session Boundary Probe (from art/lead_bigpickle.txt)
- NEXT(hypotheses-bigpickle.txt): PROBE: GET https://www.obi.de/account/ and GET https://www.obi.de/account/api/ with User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KH
- LEARN: ACCEPTED AUTH @ www.obi.de: /account/api/public/jwt/validate reachable from edge with browser UA — GET/HEAD 200 (text/javascript, len 0) + Set-Cookie expires ob
- LEARN: ACCEPTED ENDPOINT-MAP @ www.obi.de: /explore/recommendations/api/internal/v6/recommendations returns 500 with browser UA — origin app live; 500 (not 401/403) in

## RANKED HYPOTHESES 2026-09-05 04:40:47 UTC
- [92] api.obi.com: MuleSoft Exchange Portal — Unauthenticated Full API Spec Download & Endpoint Enumeration via S3 Signed URLs (from art/lead_nemotron3.txt)
- NEXT(hypotheses-nemotron3.txt): PROBE: GET https://www.obi.de/account/ and GET https://www.obi.de/account/api/ with User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KH
- LEARN: ACCEPTED AUTH @ www.obi.de: /account/api/public/jwt/validate reachable from edge with browser UA — GET/HEAD 200 (text/javascript, len 0) + Set-Cookie expires ob
- LEARN: ACCEPTED ENDPOINT-MAP @ www.obi.de: /explore/recommendations/api/internal/v6/recommendations returns 500 with browser UA — origin app live; 500 (not 401/403) in

## RANKED HYPOTHESES 2026-09-05 08:56:11 UTC
- [85] api.obi.com: MuleSoft API Portal Public Exposure — Unauthenticated API Documentation & Seller Onboarding (from art/lead_bigpickle.txt)
- NEXT(hypotheses-bigpickle.txt): PROBE: GET https://api.obi.com/e9d97593-77e2-4c1d-983c-b4593b3393ed/order-service-management-api/ with browser-like Accept headers and check if API documentatio
- LEARN: ACCEPTED MISCONFIG @ api.obi.com: Public MuleSoft Exchange portal exposes marketplace API documentation (order, product, price, inventory, transactions, seller)
- LEARN: ACCEPTED AUTH @ www.obi.de: JWT validation endpoint path confirmed in production JavaScript — viable test target for alg confusion with authenticated session.
- LEARN: REJECTED ENDPOINT-MAP @ www.obi.de: All /api/* paths return 404 at CloudFront edge — origin routing requires browser-level session/cookies. Cannot enumerate liv

## RANKED HYPOTHESES 2026-09-05 12:23:25 UTC
- [55] www.obi.de/explore/recommendations/api/internal/v6/: Internal Recommendations API — Potential IDOR via Product IDs (from art/lead_bigpickle.txt)
- NEXT(hypotheses-bigpickle.txt): PROBE: GET https://api.obi.com/e9d97593-77e2-4c1d-983c-b4593b3393ed/order-service-management-api/ with Accept: text/html,application/xhtml+xml and User-Agent: M
- LEARN: ACCEPTED MISCONFIG @ api.obi.com: seller-data-hub-service/api/v1/public/ answers unauthenticated — /public/de/feature-toggle 200 (internal SOA.* flags + project
- LEARN: ACCEPTED ENDPOINT-MAP @ assets.obi.de: correct bundle path is /seller-onboarding/seller-side-panel/resources/index-BUGS3Fny.js → 200 (230KB); all prior 404s use
- LEARN: ACCEPTED AUTH @ api.obi.com: seller bundle fetches seller-data-hub /public/ with mode:cors + credentials:include from assets.obi.de origin; base api.obi.com is 

## RANKED HYPOTHESES 2026-09-05 15:36:13 UTC
- [88] api.obi.com/trx-api/fulfillmentsellersteering/seller-data-hub-service/api/v1/public/: Seller Data Hub Public API — Unauthenticated SOA Flag Exposure & Cross-Seller Enumeration Oracle (from art/lead_nemotron3.txt)
- [80] api.obi.com/trx-api/fulfillmentsellersteering/seller-data-hub-service/api/v1/public/{cc}/seller-side-panel/{id}: Seller Data-Hub /public/ Seller Registry — Full trxId Enumeration (imprint + business data) (from art/lead_bigpickle.txt)
- NEXT(hypotheses-bigpickle.txt): PROBE: Via MuleSoft portal HTML (api.obi.com, CORS:*), re-extract current S3 signed URL for the **seller-data-hub-service** API spec (OAS/RAML) and grep it for 
- NEXT(hypotheses-nemotron3.txt): PROBE: GET https://api.obi.com/trx-api/fulfillmentsellersteering/seller-data-hub-service/api/v1/public/de/feature-toggle with Accept: application/json, User-Age
- LEARN: ACCEPTED MISCONFIG @ api.obi.com: seller-data-hub-service/api/v1/public is a full unauthenticated seller registry — /public/{cc}/seller-side-panel/{trxId} retur
- LEARN: ACCEPTED ENDPOINT-MAP @ api.obi.com: /public tree is exactly feature-toggle + seller-side-panel/{id} + imprint-documents/{key}/{cp|gtc} (35 candidate names 404)
- LEARN: ACCEPTED MISCONFIG @ api.obi.com: seller-data-hub-service/api/v1/public/ answers unauthenticated — /public/de/feature-toggle 200 (internal SOA.* flags + project
- LEARN: ACCEPTED ENDPOINT-MAP @ assets.obi.de: correct bundle path is /seller-onboarding/seller-side-panel/resources/index-BUGS3Fny.js → 200 (230KB); all prior 404s use
- LEARN: ACCEPTED AUTH @ api.obi.com: seller bundle fetches seller-data-hub /public/ with mode:cors + credentials:include from assets.obi.de origin; base api.obi.com is 
- LEARN: ACCEPTED MISCONFIG @ www.obi.de: Baqend Speed Kit + customer-center/regi-auth bundles confirmed in live DOM — BaaS integration active in production (supersedes 

## RANKED HYPOTHESES 2026-09-05 17:39:39 UTC
- [92] api.obi.com/trx-api/fulfillmentsellersteering/seller-data-hub-service/api/v1/public/: Seller Data Hub Public API — Unauthenticated Seller Registry Enumeration & PII Exposure (from art/lead_nemotron3.txt)
- [70] www.obi.de/account/api/public/jwt/validate: JWT Validation Endpoint — Algorithm Confusion / Session Boundary Probe (from art/lead_bigpickle.txt)
- NEXT(hypotheses-bigpickle.txt): PROBE: GET https://api.obi.com/ with User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/125.0.0.0 Safari/537.36
- NEXT(hypotheses-nemotron3.txt): PROBE: GET https://api.obi.com/trx-api/fulfillmentsellersteering/seller-data-hub-service/api/v1/public/de/seller-side-panel/1 with Accept: application/json, Use
- LEARN: ACCEPTED MISCONFIG @ api.obi.com: seller-data-hub-service/api/v1/public/ is a full unauthenticated seller registry — /public/{cc}/seller-side-panel/{trxId} retu
- LEARN: ACCEPTED ENDPOINT-MAP @ api.obi.com: /public tree is exactly feature-toggle + seller-side-panel/{id} + imprint-documents/{key}/{cp|gtc} (35 candidate names 404)
- LEARN: ACCEPTED AUTH @ api.obi.com: seller bundle fetches seller-data-hub /public/ with mode:cors + credentials:include from assets.obi.de origin; base api.obi.com is 
- LEARN: ACCEPTED MISCONFIG @ api.obi.com: seller-data-hub-service/api/v1/public is a full unauthenticated seller registry — /public/{cc}/seller-side-panel/{trxId} retur
- LEARN: ACCEPTED ENDPOINT-MAP @ api.obi.com: /public tree is exactly feature-toggle + seller-side-panel/{id} + imprint-documents/{key}/{cp|gtc} (35 candidate names 404)
- LEARN: ACCEPTED ENDPOINT-MAP @ assets.obi.de: correct bundle path is /seller-onboarding/seller-side-panel/resources/index-BUGS3Fny.js → 200 (230KB); all prior 404s use
- LEARN: ACCEPTED AUTH @ api.obi.com: seller bundle fetches seller-data-hub /public/ with mode:cors + credentials:include from assets.obi.de origin; base api.obi.com is 
- LEARN: ACCEPTED MISCONFIG @ www.obi.de: Baqend Speed Kit + customer-center/regi-auth bundles confirmed in live DOM — BaaS integration active in production (supersedes 
- LEARN: ACCEPTED AUTH @ www.obi.de: /account/api/public/jwt/validate reachable from edge with browser UA — GET/HEAD 200 (text/javascript, len 0) + Set-Cookie expires ob
- LEARN: ACCEPTED ENDPOINT-MAP @ www.obi.de: /explore/recommendations/api/internal/v6/recommendations returns 500 with browser UA — origin app live; 500 (not 401/403) in

## RANKED HYPOTHESES 2026-09-05 19:36:01 UTC
- [95] api.obi.com/trx-api/fulfillmentsellersteering/seller-data-hub-service/api/v1/public/: Seller Data Hub Public API — Unauthenticated Seller Registry Enumeration & PII Exposure (from art/lead_nemotron3.txt)
- NEXT(hypotheses-nemotron3.txt): PROBE: GET https://api.obi.com/trx-api/fulfillmentsellersteering/seller-data-hub-service/api/v1/public/de/seller-side-panel/1 with Accept: application/json, Use
- LEARN: ACCEPTED MISCONFIG @ api.obi.com: seller-data-hub-service/api/v1/public/ is a full unauthenticated seller registry — /public/{cc}/seller-side-panel/{trxId} retu
- LEARN: ACCEPTED ENDPOINT-MAP @ api.obi.com: /public tree is exactly feature-toggle + seller-side-panel/{id} + imprint-documents/{key}/{cp|gtc} (35 candidate keys teste
- LEARN: ACCEPTED ENDPOINT-MAP @ assets.obi.de: correct bundle path is /seller-onboarding/seller-side-panel/resources/index-BUGS3Fny.js → 200 (230KB); all prior 404s use
- LEARN: ACCEPTED AUTH @ api.obi.com: seller bundle fetches seller-data-hub /public/ with mode:cors + credentials:include from assets.obi.de origin; base api.obi.com is 
- LEARN: ACCEPTED MISCONFIG @ www.obi.de: Baqend Speed Kit + customer-center/regi-auth bundles confirmed in live DOM — BaaS integration active in production (supersedes 
- LEARN: ACCEPTED AUTH @ www.obi.de: /account/api/public/jwt/validate reachable from edge with browser UA — GET/HEAD 200 (text/javascript, len 0) + Set-Cookie expires ob
- LEARN: ACCEPTED ENDPOINT-MAP @ www.obi.de: /explore/recommendations/api/internal/v6/recommendations returns 500 with browser UA — origin app live; 500 (not 401/403) in
