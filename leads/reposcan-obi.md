## REPOSCAN 2026-09-03 17:15:38 UTC
TARGET_ORG not configured for obi; skipping public-org deep scan.
## REPOSCAN 2026-09-03 19:57:29 UTC
class: OTHER
asset: N/A
confidence: 100
reasoning: The user specified "no org candidates" in the scope definition. No GitHub orgs or user accounts matching OBI Group Holding were found via unauthenticated GitHub API search.
impact: N/A
verify_steps: Request the specific repository URLs or GitHub organization name to audit from the program owner.
TARGET_ORG not configured for obi; skipping public-org deep scan.
## REPOSCAN 2026-09-03 22:31:10 UTC
[HYP] No OBI-owned public GitHub repositories exist to audit
class: OTHER
asset: N/A
confidence: 100
reasoning: >
impact: N/A — source-code audit surface is empty
verify_steps: >
TARGET_ORG not configured for obi; skipping public-org deep scan.
## REPOSCAN 2026-09-04 00:27:19 UTC
TARGET_ORG not configured for obi; skipping public-org deep scan.
## REPOSCAN 2026-09-04 05:07:26 UTC
TARGET_ORG not configured for obi; skipping public-org deep scan.
## REPOSCAN 2026-09-04 09:44:59 UTC
TARGET_ORG not configured for obi; skipping public-org deep scan.
## REPOSCAN 2026-09-04 14:10:18 UTC
[HYP] No OBI-owned public GitHub repositories exist to audit
class: OTHER
asset: (none)
confidence: 100
reasoning: |
impact: N/A
verify_steps: Confirm via GitHub search "org:obi-ds" and
TARGET_ORG not configured for obi; skipping public-org deep scan.
## REPOSCAN 2026-09-04 17:40:28 UTC
TARGET_ORG not configured for obi; skipping public-org deep scan.
## REPOSCAN 2026-09-04 19:54:59 UTC
TARGET_ORG not configured for obi; skipping public-org deep scan.
## REPOSCAN 2026-09-04 22:17:35 UTC
TARGET_ORG not configured for obi; skipping public-org deep scan.
## REPOSCAN 2026-09-05 00:14:46 UTC
TARGET_ORG not configured for obi; skipping public-org deep scan.
## REPOSCAN 2026-09-05 04:45:14 UTC
class: OTHER
asset: obi-services/obi-operations-portal/scripts/bootstrap-admin.ts:28
confidence: 70
reasoning: Bootstrap script contains hardcoded email `michael.j@techguys.work` and name `Michael J.` in source code. While secrets use `env()` substitution, the admin identity is exposed in the public repo. Could aid targeted phishing or social engineering against this admin.
impact: Low
verify_steps: Confirm `michael.j@techguys.work` is an active OBI admin email; check if this account exists on OBI's identity provider.
class: MISCONFIG
asset: obi-services/obi-operations-portal/supabase/config.toml:176,226
confidence: 65
reasoning: `enable_signup = true` and `enable_confirmations = false` in Supabase config. The sign-up form (`components/sign-up-form.tsx`) allows anyone to create accounts without email verification. If the deployed portal doesn't have additional server-side restrictions, unauthorized users could register accounts.
impact: Medium
verify_steps: Attempt to register a new account on the deployed portal; check if new users get auto-assigned roles or are restricted.
class: MISCONFIG
asset: obi-services/obi-operations-portal/supabase/config.toml:182-185
confidence: 80
reasoning: `minimum_password_length = 6` and `password_requirements = ""` (no complexity requirements). Industry standard recommends minimum 8 characters with complexity. Weak passwords on an operations portal managing clients and agent assignments increase account compromise risk.
impact: Low
verify_steps: Attempt to set a weak password (e.g., "123456") during registration or password change.
class: MISCONFIG
asset: obi-services/obi-operations-portal/supabase/config.toml:302-308
confidence: 75
reasoning: TOTP and phone MFA are both disabled (`enroll_enabled = false`, `verify_enabled = false`). For an internal operations portal with admin/supervisor roles managing clients and assignments, MFA should be enforced.
impact: Medium
verify_steps: Check if MFA enrollment is available in the UI; verify if any privileged accounts have MFA enabled.
class: OTHER
asset: obi-services/obi-operations-portal/lib/supabase/admin.ts:31-34
confidence: 50
reasoning: The `createAdminClient()` uses `SUPABASE_SECRET_KEY` which bypasses all RLS policies. This is by design for admin operations, but if the secret key leaks (e.g., via env var exposure, log leakage, or compromised server), an attacker would have unrestricted database access. The code comment explicitly warns: "The secret key bypasses Row Level Security."
impact: High (if key leaks), Low (as currently configured)
verify_steps: Verify `SUPABASE_SECRET_KEY` is not exposed in logs, error messages, or client-side code; check Supabase dashboard for RLS bypass audit logs.
class: OTHER
asset: obi-services/obi-operations-portal/supabase/config.toml:399-405
confidence: 40
reasoning: The `[experimental]` section references `s3_access_key = "env(S3_ACCESS_KEY)"` and `s3_secret_key = "env(S3_SECRET_KEY)"` for S3-backed storage. While these use env var substitution (correct), the presence of this config in a public repo reveals the infrastructure pattern. An attacker now knows to look for `S3_ACCESS_KEY` and `S3_SECRET_KEY` environment variables.
impact: Low
verify_steps: Confirm these env vars are properly scoped and not shared with other services; verify S3 buckets are not publicly accessible.
class: SSRF
asset: obi-services/obi-operations-portal/app/dashboard/clients/manage/route.ts:23-28
confidence: 35
reasoning: The `buildRedirectUrl` function constructs redirect URLs using `x-forwarded-host` header: `const forwardedHost = request.headers.get("x-forwarded-host");`. If the reverse proxy doesn't strip/validate this header, an attacker could inject a malicious host to redirect users to an attacker-controlled site after form submission. However, Next.js middleware may mitigate this.
impact: Low
verify_steps: Test if `x-forwarded-host` header is trusted by the application; attempt to inject a malicious host value and observe redirect behavior.
class: OTHER
asset: obi-services/obi-operations-portal/app/dashboard/clients/[clientCode]/page.tsx:64-71
confidence: 30
reasoning: The `formatDate` function hardcodes timezone to `Asia/Manila`. For a German DIY retail company (OBI Group), this is unusual and suggests the portal may be developed/maintained by a team in the Philippines. This is a minor operational security concern as it reveals internal team location.
impact: Informational
verify_steps: Confirm if OBI has operations or development team in the Philippines.
TARGET_ORG not configured for obi; skipping public-org deep scan.
## REPOSCAN 2026-09-05 08:41:13 UTC
TARGET_ORG not configured for obi; skipping public-org deep scan.
## REPOSCAN 2026-09-05 12:06:33 UTC
TARGET_ORG not configured for obi; skipping public-org deep scan.
## REPOSCAN 2026-09-05 15:23:26 UTC
TARGET_ORG not configured for obi; skipping public-org deep scan.
## REPOSCAN 2026-09-05 17:39:35 UTC
TARGET_ORG not configured for obi; skipping public-org deep scan.
