# Knowledge Base (seed)
- 2026-09-03 ACCEPTED MISCONFIG @ api.obi.com: Public MuleSoft Exchange portal exposes marketplace API documentation (order, product, price, inventory, transactions, seller) with CORS: * — real misconfiguration enabling reconnaissance.
- 2026-09-03 ACCEPTED AUTH @ www.obi.de: JWT validation endpoint path confirmed in production JavaScript — viable test target for alg confusion with authenticated session.
- 2026-09-03 REJECTED ENDPOINT-MAP @ www.obi.de: All /api/* paths return 404 at CloudFront edge — origin routing requires browser-level session/cookies. Cannot enumerate live backend APIs from curl alone.
- 2026-09-03 REJECTED ENDPOINT-MAP @ www.obi.de: CONFIRMED — /account/api/public/jwt/validate and /explore/recommendations/api/internal/v6/ both return 404 to HEAD/curl; origin routing requires browser UA + cookies per CloudFront edge behavior
- 2026-09-03 ACCEPTED MISCONFIG @ assets.obi.de: Seller onboarding JS bundle (index-BUGS3Fny.js) publicly accessible on S3-backed CDN with CORS: * — no auth gate, enables static analysis for secrets/endpoints
- 2026-09-03 REJECTED ENDPOINT-MAP @ www.obi.de: CONFIRMED — /account/api/public/jwt/validate and /explore/recommendations/api/internal/v6/ both return 404 to HEAD/curl; origin routing requires browser UA + cookies per CloudFront edge behavior
- 2026-09-03 ACCEPTED MISCONFIG @ assets.obi.de: Seller onboarding JS bundle (index-BUGS3Fny.js) publicly accessible on S3-backed CDN with CORS: * — no auth gate, enables static analysis for secrets/endpoints
- 2026-09-03 ACCEPTED AUTH @ api.live.app.obi.de: /v1/ base path returns 200, /v1/health and /v1/auth/login return 401 — mobile API v1 confirmed live with auth-gated endpoints
- 2026-09-03 CHANGED MISCONFIG @ assets.obi.de: Seller onboarding JS bundle (index-BUGS3Fny.js) now returns 404 — previously accessible, likely rotated/removed; need to discover new bundle filename
