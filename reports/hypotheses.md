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
