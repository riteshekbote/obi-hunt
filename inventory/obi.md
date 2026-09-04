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
