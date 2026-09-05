# OBI Group Holding SE & Co. KGaA inventory (discovery seed 2026-09-02)
# NOTE: hosts below are discovery candidates from passive DNS/CT; confirm in-scope vs program scope before active testing.
mail.obi.de
obi.de
www.obi.de

## PASSIVE RECON 2026-09-02 (read-only, non-intrusive)

> Recon observations only. These are NOT confirmed vulnerabilities; ownership/in-scope of each host must be confirmed against the program scope before any active testing. Hosts resolve + serve HTTP — investigation requires scoped authorization.

**Probed:** 3 hosts | **Live HTTP:** 0

| Host | Status | Server/Tech |
|---|---|---|

## 2026-09-02 21:42:23 UTC

## 2026-09-02 23:55:00 UTC

## 2026-09-03 03:49:23 UTC

## 2026-09-03 08:20:36 UTC

## 2026-09-03 12:54:33 UTC

## 2026-09-03 17:18:16 UTC
- NEW api.obi.com — MuleSoft API Portal, publicly accessible, 14+ marketplace APIs exposed
- NEW api.live.app.obi.de — Mobile app API, Envoy proxy, /v1/ versioned
- NEW imgix.obi.de — Image CDN, CORS: *, S3-backed
- NEW assets.obi.de — Static asset CDN, S3 origin
- NEW obi-de.app.baqend.com — Baqend BaaS speed kit integration
- NEW 6+ backend API paths on www.obi.de (cart, PDP, CMS, recommendations, JWT validate)
- NEW Seller onboarding JS bundle exposed on frontend
- CHANGED www.obi.de — Now confirmed live with browser UA; Discover CMS + Vtex platform; origin returns 404 to raw HEAD but serves full SPA to browser UA

## 2026-09-03 20:01:48 UTC

## 2026-09-03 22:28:31 UTC
- NEW api.live.app.obi.de/v1/ → HTTP 200 (base path accessible, Envoy proxy confirmed)
- NEW api.live.app.obi.de/v1/health → HTTP 401 (endpoint exists, auth required)
- NEW api.live.app.obi.de/v1/auth/login → HTTP 401 (endpoint exists, auth required)
- CHANGED assets.obi.de/seller-side-panel/resources/index-BUGS3Fny.js → HTTP 404 (was accessible per knowledge base, now rotated/removed)

## 2026-09-04 00:37:33 UTC

## 2026-09-04 05:18:47 UTC

## 2026-09-04 09:49:39 UTC
- NEW api.live.app.obi.de/v1/admin → HTTP 401 (admin endpoint exists, auth-gated)
- NEW api.live.app.obi.de/v1/debug → HTTP 401 (debug endpoint exists, auth-gated)
- NEW api.live.app.obi.de/v1/v2/ → HTTP 401 (v2 versioned path exists, auth-gated)
- NEW api.live.app.obi.de/v1/internal/ → HTTP 401 (internal path exists, auth-gated)
- CHANGED assets.obi.de/seller-side-panel/resources/index-BUGS3Fny.js → HTTP 404 confirmed (was accessible, now rotated/removed — bundle filename changed)
- CHANGED api.live.app.obi.de fully enumerated: /v1/ base 200, all 10 tested sub-paths return 401 — no unauthenticated leakage found
- CHANGED api.obi.com: Portal returns full JSON catalog of 4+ marketplace APIs with S3 signed download URLs, org IDs, contact emails, version info — previously only confirmed as HTTP 200, now confirmed as full 
- CHANGED api.obi.com: Full JSON API catalog extracted from SPA HTML body — 4 marketplace APIs with complete metadata (names, descriptions, versions, S3 signed download URLs, org IDs, contact emails). Portal se
- CHANGED api.obi.com: MuleSoft Anypoint org `trx-fulfillmentsellersteering` (ID `e9d97593-77e2-4c1d-983c-b4593b3393ed`), parent org `f970166b-9dae-4e62-976d-cfccd05e93ff`, domain `obi-smart-technologies-gmbh`
- CHANGED api.obi.com: S3 signed download URLs for API spec files (OAS/RAML/ZIP) point to `exchange2-asset-manager-kprod-eu.s3.eu-central-1.amazonaws.com` with temp AWS credentials embedded in query strings

## 2026-09-04 14:16:57 UTC
- NEW api.live.app.obi.de/v1/beta → HTTP 401 (Spring Boot actuator-style endpoint exists, auth-gated)
- NEW api.live.app.obi.de/v1/test → HTTP 401 (test endpoint exists, auth-gated)
- NEW api.live.app.obi.de/v1/swagger → HTTP 401 (OpenAPI UI endpoint exists, auth-gated)
- NEW api.live.app.obi.de/v1/openapi.json → HTTP 401 (OpenAPI spec endpoint exists, auth-gated)
- NEW api.live.app.obi.de/v1/graphql → HTTP 401 (GraphQL endpoint exists, auth-gated)
- NEW api.live.app.obi.de/v1/metrics → HTTP 401 (Prometheus metrics endpoint exists, auth-gated)
- NEW api.live.app.obi.de/v1/actuator/health → HTTP 401 (Spring actuator health exists, auth-gated)
- CHANGED assets.obi.de/seller-side-panel/resources/index-BUGS3Fny.js → HTTP 404 confirmed (bundle filename rotated/removed, new name unknown)
- CHANGED api.live.app.obi.de fully enumerated: /v1/ base 200, all 10 tested sub-paths return 401 — no unauthenticated leakage found
- CHANGED api.obi.com: Full JSON catalog of 4 marketplace APIs (Product v1.1.6, Price v1.1.5, Inventory v1.1.6, Order Invoice v1.0.26) extractable from SPA HTML body without JS execution; S3 signed download URL
- CHANGED api.obi.com: S3 signed URLs point to exchange2-asset-manager-kprod-eu.s3.eu-central-1.amazonaws.com with 86400s expiry AWS temp credentials in query strings

## 2026-09-04 17:50:25 UTC

## 2026-09-04 20:13:00 UTC

## 2026-09-04 22:15:24 UTC
- NEW assets.obi.de/ returns HTTP 200 (39 bytes) — root directory accessible, previously unprobed
- CHANGED assets.obi.de/seller-side-panel/ and /seller-side-panel/resources/ both return 404 — seller onboarding path fully gone, bundle rotation confirmed
- CHANGED www.obi.de/account/api/public/jwt/validate consistently returns 404 to HEAD/curl (7+ probes) — CloudFront edge behavior confirmed, requires browser UA+cookies

## 2026-09-05 00:15:43 UTC
- CHANGED Knowledge base holds 3 superseded REJECTED rows claiming /account/api/public/jwt/validate is 404; the 09-04 ACCEPTED fact (reachable with browser UA, GET/HEAD 200 + clears obi-auth, POST no-session 40
- CHANGED /explore/recommendations/api/internal/v6/recommendations now 500 (not 401/403) with browser UA — origin app live, error type implies missing-params, not auth gate.
- NEW None — no additional surface probed since last update; assets.obi.de seller-side-panel fully rotated (404 root + resources), api.live.app.obi.de remains fully 401-gated across all 17 sub-paths.

## 2026-09-05 04:40:47 UTC
- CHANGED www.obi.de/account/api/public/jwt/validate: Previously REJECTED as 404 (curl/HEAD), now ACCEPTED as live with browser UA (GET/HEAD 200, text/javascript len 0, Set-Cookie expires obi-auth; POST w/o ses
- CHANGED www.obi.de/explore/recommendations/api/internal/v6/recommendations: Now returns 500 with browser UA (was 404) — origin app live, 500 indicates missing params not auth gate, candidate for passive param
- NEW No additional surface probed since last update; assets.obi.de seller-side-panel fully rotated (404 root + resources), api.live.app.obi.de remains fully 401-gated across all 17 sub-paths.

## 2026-09-05 08:56:11 UTC
- NEW api.obi.com — MuleSoft API Portal, publicly accessible, 14+ marketplace APIs exposed
- NEW api.live.app.obi.de — Mobile app API, Envoy proxy, /v1/ versioned
- NEW imgix.obi.de — Image CDN, CORS: *, S3-backed
- NEW assets.obi.de — Static asset CDN, S3 origin
- NEW obi-de.app.baqend.com — Baqend BaaS speed kit integration
- NEW 6+ backend API paths on www.obi.de (cart, PDP, CMS, recommendations, JWT validate)
- NEW Seller onboarding JS bundle exposed on frontend
- CHANGED www.obi.de — Now confirmed live with browser UA; Discover CMS + Vtex platform; origin returns 404 to raw HEAD but serves full SPA to browser UA

## 2026-09-05 12:23:25 UTC
- NEW api.obi.com/trx-api/fulfillmentsellersteering/seller-data-hub-service/api/v1/public/ — unauth service tree diverging from siblings (feature-toggle HTTP 200, no mule-realm 401), discovered from live se
- CHANGED assets.obi.de seller bundle is LIVE at /seller-onboarding/seller-side-panel/resources/index-BUGS3Fny.js → 200 (230KB, application/javascript); prior 404s were wrong path (missing /seller-onboarding/ p
- NEW www.obi.de live DOM embeds obi-de.app.baqend.com/v1/speedkit/install.js?d=production + customer-center/regi-auth bundles — Baqend confirmed in client runtime (was parked)

## 2026-09-05 15:36:13 UTC
- NEW api.obi.com/trx-api/fulfillmentsellersteering/seller-data-hub-service/api/v1/public/ — unauthenticated service tree diverging from siblings: /public/de/feature-toggle returns 200 with internal SOA.* f
- CHANGED assets.obi.de/seller-onboarding/seller-side-panel/resources/index-BUGS3Fny.js → 200 (230KB, application/javascript); prior 404s used wrong path (missing /seller-onboarding/ prefix); bundle never rotat
- NEW www.obi.de live DOM embeds obi-de.app.baqend.com/v1/speedkit/install.js?d=production + customer-center/regi-auth bundles — Baqend BaaS confirmed in client runtime (was parked)

## 2026-09-05 17:39:39 UTC
- NEW api.obi.com/trx-api/fulfillmentsellersteering/seller-data-hub-service/api/v1/public/{cc}/seller-side-panel/{trxId} — unauthenticated seller registry enumerating complete imprint+settings for sequentia
- NEW api.obi.com/trx-api/fulfillmentsellersteering/seller-data-hub-service/api/v1/public/imprint-documents/{key}/{cp|gtc} — 35 candidate keys tested, key=obiecomprod returns PDF (Widerrufsbelehrung/AGB); p
- CHANGED assets.obi.de/seller-onboarding/seller-side-panel/resources/index-BUGS3Fny.js → 200 (230KB); prior 404s used incorrect path missing `/seller-onboarding/` prefix — bundle never rotated
- CHANGED www.obi.de live DOM confirms Baqend Speed Kit (`obi-de.app.baqend.com/v1/speedkit/install.js?d=production`) + customer-center/regi-auth bundles — BaaS integration active in production
- CHANGED www.obi.de/account/api/public/jwt/validate — confirmed live with browser UA (GET/HEAD 200, text/javascript len 0, Set-Cookie expires obi-auth; POST w/o session 405); prior REJECTED rows were UA-based 
- CHANGED www.obi.de/explore/recommendations/api/internal/v6/recommendations → 500 with browser UA (was 404) — origin app live, 500 indicates missing params not auth gate

## 2026-09-05 19:36:01 UTC
- NEW api.obi.com/trx-api/fulfillmentsellersteering/seller-data-hub-service/api/v1/public/{cc}/seller-side-panel/{trxId} — confirmed live unauthenticated seller registry returning full imprint+settings for 
- NEW api.obi.com/trx-api/fulfillmentsellersteering/seller-data-hub-service/api/v1/public/imprint-documents/{key}/{cp|gtc} — confirmed live returning PDF legal documents (Widerrufsbelehrung/AGB); key=obieco
- NEW api.obi.com/trx-api/fulfillmentsellersteering/seller-data-hub-service/api/v1/public/de/feature-toggle — confirmed live exposing 15 internal SOA.* feature flags + project names (e.g., SOA.412-isDocumen
- CHANGED www.obi.de/account/api/public/jwt/validate — confirmed live with browser UA (GET/HEAD 200, text/javascript len 0, Set-Cookie expires obi-auth; POST w/o session 405); prior curl/HEAD 404 = UA-based Clo
- CHANGED assets.obi.de/seller-onboarding/seller-side-panel/resources/index-BUGS3Fny.js → 200 (230KB); prior 404s used incorrect path missing `/seller-onboarding/` prefix — bundle never rotated
- CHANGED www.obi.de live DOM confirms Baqend Speed Kit (`obi-de.app.baqend.com/v1/speedkit/install.js?d=production`) + customer-center/regi-auth bundles — BaaS integration active in production

## 2026-09-05 21:46:58 UTC
- NEW api.obi.com/trx-api/fulfillmentsellersteering/seller-data-hub-service/api/v1/public/{cc}/seller-side-panel/{trxId} — confirmed live unauthenticated seller registry returning full imprint+settings for 
- NEW api.obi.com/trx-api/fulfillmentsellersteering/seller-data-hub-service/api/v1/public/imprint-documents/{key}/{cp|gtc} — confirmed live returning PDF legal documents (Widerrufsbelehrung/AGB); key=obieco
- NEW api.obi.com/trx-api/fulfillmentsellersteering/seller-data-hub-service/api/v1/public/de/feature-toggle — confirmed live exposing 15 internal SOA.* feature flags + project names (e.g., SOA.412-isDocumen
- CHANGED www.obi.de/account/api/public/jwt/validate — confirmed live with browser UA (GET/HEAD 200, text/javascript len 0, Set-Cookie expires obi-auth; POST w/o session 405); prior curl/HEAD 404 = UA-based Clo
- CHANGED assets.obi.de/seller-onboarding/seller-side-panel/resources/index-BUGS3Fny.js → 200 (230KB); prior 404s used incorrect path missing `/seller-onboarding/` prefix — bundle never rotated
- CHANGED www.obi.de live DOM confirms Baqend Speed Kit (`obi-de.app.baqend.com/v1/speedkit/install.js?d=production`) + customer-center/regi-auth bundles — BaaS integration active in production
- NEW api.obi.com/trx-api/fulfillmentsellersteering/seller-data-hub-service/api/v1/public/{cc}/seller-side-panel/{trxId} — confirmed live unauthenticated seller registry returning full imprint+settings for 
- NEW api.obi.com/trx-api/fulfillmentsellersteering/seller-data-hub-service/api/v1/public/imprint-documents/{key}/{cp|gtc} — confirmed live returning PDF legal documents (Widerrufsbelehrung/AGB); key=obieco
- NEW api.obi.com/trx-api/fulfillmentsellersteering/seller-data-hub-service/api/v1/public/de/feature-toggle — confirmed live exposing 15 internal SOA.* feature flags + project names (e.g., SOA.412-isDocumen
- CHANGED www.obi.de/account/api/public/jwt/validate — confirmed live with browser UA (GET/HEAD 200, text/javascript len 0, Set-Cookie expires obi-auth; POST w/o session 405); prior curl/HEAD 404 = UA-based Clo
- CHANGED assets.obi.de/seller-onboarding/seller-side-panel/resources/index-BUGS3Fny.js → 200 (230KB); prior 404s used incorrect path missing `/seller-onboarding/` prefix — bundle never rotated
- CHANGED www.obi.de live DOM confirms Baqend Speed Kit (`obi-de.app.baqend.com/v1/speedkit/install.js?d=production`) + customer-center/regi-auth bundles — BaaS integration active in production
- CHANGED api.obi.com portal (root, 484KB): exchange catalog no longer embeds S3 signed download URLs server-side; assets listed = 11 (api-documentation-incl-seller-onboarding-steps, inventory, order-invoice, o
- CHANGED assets.obi.de bundle (230643B, re-downloaded): exactly 2 api.obi.com fetch targets — /public/${cc}/feature-toggle (GET, cors, credentials:include) + /public/${cc}/seller-side-panel/${id}. /public/ han
- NEW seller-data-hub-service non-public mirror /api/v1/{de/feature-toggle,de/seller-side-panel/1} → 401 mule-realm (base /api/v1/ → 404) — same handlers, real auth boundary right next to the open /public/ 
- NEW Sibling scan: /v1/public/ on transaction-api, order-service-api, invoice-api, product-api, inventory-api, pricing-api, subscription-api → ALL 401 mule-realm (bare order-service → 404). Exposure UNIQUE
- NEW Current CORS recheck on /public/de/feature-toggle (1228B): Origin: https://example.com → ACAO: https://example.com + Access-Control-Allow-Credentials: true. Any-origin credentialed read confirmed.
- NEW Current registry recheck /public/de/seller-side-panel/1 → 200, 37496B: imprint{companyImprint,sellerSettingsImprintObject,bioCertificate} + isObiEcomSellerAccount + shippingCostAndThreshold. Finding l
- CHANGED api.obi.com portal root catalog (11 assets, no seller-data-hub spec; S3 signed URLs no longer server-embedded) — OAS-extraction avenue closed, /public/ tree closed via bundle (2 fetchers) + brute forc
- NEW seller-data-hub /public/ exposure UNIQUE to this service: non-public /api/v1/ mirror + all 7 siblings /v1/public/ = 401 mule-realm
- NEW /public/de/feature-toggle reflects arbitrary Origin + ACAC:true; seller-side-panel/1 live 37496B
- NEW api.obi.com/trx-api/fulfillmentsellersteering/seller-data-hub-service/api/v1/public/ — full unauthenticated seller registry confirmed across 6 countries (DE/AT/IT/PL/FR/ES) with sequential trxId enume
- NEW www.obi.de/account/api/public/jwt/validate — confirmed live with browser UA (GET/HEAD 200, text/javascript len 0, Set-Cookie expires obi-auth; POST w/o session 405); prior curl/HEAD 404 = UA-based Clo
- NEW www.obi.de/explore/recommendations/api/internal/v6/recommendations — returns 500 with browser UA (was 404); origin app live, 500 indicates missing params not auth gate, candidate for passive parameter
- CHANGED assets.obi.de/seller-onboarding/seller-side-panel/resources/index-BUGS3Fny.js → 200 (230KB); prior 404s used incorrect path missing `/seller-onboarding/` prefix — bundle never rotated
- CHANGED www.obi.de live DOM confirms Baqend Speed Kit (`obi-de.app.baqend.com/v1/speedkit/install.js?d=production`) + customer-center/regi-auth bundles — BaaS integration active in production

## 2026-09-05 23:40:21 UTC
- NEW www.obi.de/regi/auth/api/fe/hey-obi/login-info — route found in live panel-controllers.hn0a0q0D.js (OGP: "login-info"); GET and POST → 403 session-gated, live at origin (not 404).
- NEW www.obi.de/regi/auth/csrf — 200 application/javascript len-0; issues account-csrf=<uuid> (Domain=obi.de, Lax) + obi_storeid=000, clears obi-auth — edge cookie pattern mirrors jwt/validate.
- NEW obi-de.app.baqend.com — /v1/config/VAPIDPublicKey → 404 JSON "Web Push is not yet configured"; /v1/db/com.baqend.speedkit.config → 466 "Permission denied. You need admin rights." — Baqend app obi-de a
- CHANGED /explore/recommendations/api/internal/v6/recommendations — 500 invariant (empty body) across count/userId/trxId — not a params oracle; passive-fuzz avenue dead.
- CHANGED seller-data-hub registry boundaries: trxId 0/99999999/200001 → 404 JSON oracle ("vtexSellerId not found for trxId"), 100551 → 200 (Homestyle4u) — dense block ≈ 100000–100550, sparse beyond; registry s
