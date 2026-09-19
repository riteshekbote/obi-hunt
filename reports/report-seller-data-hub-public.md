# Unauthenticated Seller Registry + Any-Origin Credentialed CORS on seller-data-hub-service /public/ (api.obi.com)

- **Program:** OBI Group Holding (bugs.olivermaicher.eu)
- **Severity:** MEDIUM (CVSS 5.3) — unauthenticated disclosure of regulated marketplace seller imprint data plus CORS `Access-Control-Allow-Credentials: true` with origin reflection
- **Asset:** `https://api.obi.com/trx-api/fulfillmentsellersteering/seller-data-hub-service/api/v1/public/`
- **Class:** MISCONFIG (broken authentication boundary) + CORS misconfiguration
- **Status:** Validated by triage run-2026-09-08-20-19; report-ready, no re-probe performed pre-submission

## Summary

The `seller-data-hub-service` exposes an unauthenticated `/public/` handler tree on the marketplace API gateway. The same handlers are mirrored behind a 401-gated `/api/v1/` path, and all seven sibling services' `/v1/public/` trees return `401 WWW-Authenticate: Basic realm="mule-realm"`. The `/public/` tree is therefore a real authentication-boundary divergence, not a platform default.

Three endpoints are reachable without any authentication and further amplify the exposure with a CORS policy that reflects arbitrary `Origin` while setting `Access-Control-Allow-Credentials: true`:

1. `/public/{cc}/feature-toggle` — internal feature-flag configuration (15 `SOA.*` flags + project names), identical across 6 countries.
2. `/public/{cc}/seller-side-panel/{trxId}` — complete seller imprint + settings records (`companyImprint`, `sellerSettingsImprintObject`, `bioCertificate`, `isObiEcomSellerAccount`, `shippingCostAndThreshold`) for sequential integer IDs.
3. `/public/imprint-documents/{key}/{cp|gtc}` — issuer-of-record legal PDFs (Widerrufsbelehrung / AGB) for `key=obiecomprod` (DE) and `key=obiecomprodat` (AT).

## Reproduction

All requests are plain unauthenticated GETs. Verified on 2026-09-05..2026-09-07 (and re-confirmed passively on 2026-09-13/09-18 for feature-toggle CORS only).

### 1. Feature-flag disclosure

```bash
curl -i 'https://api.obi.com/trx-api/fulfillmentsellersteering/seller-data-hub-service/api/v1/public/de/feature-toggle'
# HTTP 200, 1228 B body listing 15 internal SOA.* flags (e.g. SOA.412-isDocumentUploadActive, project names)
# Identical response for /public/{at,it,pl,fr,es}/feature-toggle
```

### 2. Unauthenticated seller registry (`seller-side-panel`)

```bash
# trxId is a sequential integer. Response includes full imprint + settings.
curl -s 'https://api.obi.com/trx-api/fulfillmentsellersteering/seller-data-hub-service/api/v1/public/de/seller-side-panel/100551'
# HTTP 200 — imprint{companyImprint, sellerSettingsImprintObject, bioCertificate} + isObiEcomSellerAccount + shippingCostAndThreshold

# Boundary oracle: out-of-range ids disclose handler semantics
curl -s '.../public/de/seller-side-panel/0'        # 404 JSON "vtexSellerId not found for trxId: 0"
curl -s '.../public/de/seller-side-panel/99999999' # 404 JSON "vtexSellerId not found for trxId: 99999999"
# Dense block ≈ 100000–100550, sparse beyond → trxId enumeration space is bounded and contiguous
```

### 3. Legal documents

```bash
curl -s '.../public/imprint-documents/obiecomprod/cp'   # HTTP 200 PDF (Widerrufsbelehrung) — key=obiecomprodat for AT
# Path traversal blocked: .../imprint-documents/obiecomprod/ct%2F.. → HTTP 403
```

### 4. CORS origin reflection + credentialed reads

```bash
curl -i -H 'Origin: https://evil.example' \
  '.../public/de/feature-toggle'        # add ACAO: https://evil.example + Access-Control-Allow-Credentials: true
curl -i -H 'Origin: https://evil.example' \
  '.../public/de/seller-side-panel/100551' # add ACAO: https://evil.example + Access-Control-Allow-Credentials: true
```

Any origin can issue credentialed cross-origin reads (the production bundle on `assets.obi.de` fetches these endpoints with `mode: cors` + `credentials: include`, so session-bearing calls are part of the design).

### 5. Gate divergence proof

```text
seller-data-hub-service/api/v1/public/{cc}/feature-toggle      → 200 (no auth)
seller-data-hub-service/api/v1/public/{cc}/seller-side-panel/1 → 200 (no auth)
seller-data-hub-service/api/v1/{cc}/feature-toggle             → 401 mule-realm   (same handler, auth-gated)
seller-data-hub-service/api/v1/{cc}/seller-side-panel/1        → 401 mule-realm   (same handler, auth-gated)

Sibling services (7) — all /v1/public/ → 401 mule-realm:
  transaction-api, order-service-api, invoice-api, product-api, inventory-api, pricing-api, subscription-api
```

The divergence is unique to `seller-data-hub-service`: its own non-public mirror requires the mule-realm Basic auth gate, while `/public/` bypasses it entirely.

## Impact

- An unauthenticated attacker can enumerate and read the imprint + settings records of the full registered seller base across 6 countries (DE/AT/IT/PL/FR/ES) using a contiguous `trxId` range — regulated disclosure data (company imprint, seller settings) not intended for public registry.
- The CORS policy (`ACAO` arbitrary origin + `ACAC: true`) allows any website to perform credentialed read requests against these endpoints on behalf of a visiting authenticated user, extending any F/E-biased reads to cross-origin scripts.
- Feature-flag and legal-document endpoints disclose internal configuration and issuer-of-record data unauthenticated.

## Remediation

- Apply the same authentication boundary as the `/api/v1/` mirror and all sibling services to every `/public/` handler (guard at gateway/CORS layer, not per-handler).
- Replace wildcard-origin reflection with an explicit allowlist of first-party origins and drop `Access-Control-Allow-Credentials: true` unless a credentials-bearing cross-origin flow genuinely requires it and is constrained to that allowlist.
- If any read of seller imprint/registry data is intended to be non-public, remove it from the `/public/` namespace rather than relying on obscurity of `trxId`.

## Evidence provenance

All evidence gathered via read-only GET probes 2026-09-05..2026-09-07, stored in the project knowledge base; re-confirmed 2026-09-13 and 2026-09-18 for `/public/de/feature-toggle` (200 + `ACAO`/`ACAC`). No customer/employee/financial PII values are reproduced in this report. No further live probing was performed prior to submission per program rules.