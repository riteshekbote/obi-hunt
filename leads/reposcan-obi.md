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
## REPOSCAN 2026-09-05 19:33:31 UTC
TARGET_ORG not configured for obi; skipping public-org deep scan.
## REPOSCAN 2026-09-05 21:46:18 UTC
TARGET_ORG not configured for obi; skipping public-org deep scan.
## REPOSCAN 2026-09-05 23:43:07 UTC
TARGET_ORG not configured for obi; skipping public-org deep scan.
## REPOSCAN 2026-09-06 04:06:41 UTC
TARGET_ORG not configured for obi; skipping public-org deep scan.
## REPOSCAN 2026-09-06 08:44:27 UTC
TARGET_ORG not configured for obi; skipping public-org deep scan.
## REPOSCAN 2026-09-06 12:47:45 UTC
TARGET_ORG not configured for obi; skipping public-org deep scan.
## REPOSCAN 2026-09-06 16:08:10 UTC
TARGET_ORG not configured for obi; skipping public-org deep scan.
## REPOSCAN 2026-09-06 18:25:15 UTC
TARGET_ORG not configured for obi; skipping public-org deep scan.
## REPOSCAN 2026-09-06 20:49:56 UTC
TARGET_ORG not configured for obi; skipping public-org deep scan.
## REPOSCAN 2026-09-06 22:50:21 UTC
TARGET_ORG not configured for obi; skipping public-org deep scan.
## REPOSCAN 2026-09-07 00:49:38 UTC
TARGET_ORG not configured for obi; skipping public-org deep scan.
## REPOSCAN 2026-09-07 05:54:28 UTC
TARGET_ORG not configured for obi; skipping public-org deep scan.
## REPOSCAN 2026-09-07 12:10:41 UTC
TARGET_ORG not configured for obi; skipping public-org deep scan.
## REPOSCAN 2026-09-07 17:47:02 UTC
TARGET_ORG not configured for obi; skipping public-org deep scan.
## REPOSCAN 2026-09-07 21:23:49 UTC
TARGET_ORG not configured for obi; skipping public-org deep scan.
## REPOSCAN 2026-09-07 23:46:14 UTC
TARGET_ORG not configured for obi; skipping public-org deep scan.
## REPOSCAN 2026-09-08 04:07:47 UTC
TARGET_ORG not configured for obi; skipping public-org deep scan.
## REPOSCAN 2026-09-08 08:56:09 UTC
TARGET_ORG not configured for obi; skipping public-org deep scan.
## REPOSCAN 2026-09-08 13:29:20 UTC
TARGET_ORG not configured for obi; skipping public-org deep scan.
## REPOSCAN 2026-09-08 17:30:55 UTC
TARGET_ORG not configured for obi; skipping public-org deep scan.
## REPOSCAN 2026-09-08 20:19:19 UTC
confidence: 100
reasoning: GitHub API search across multiple query variants returned zero public repositories owned by OBI Group or subsidiaries. The org either has no public repos or uses a non-discoverable GitHub org name.
impact: N/A
verify_steps: None needed - no source code to audit.
TARGET_ORG not configured for obi; skipping public-org deep scan.
## REPOSCAN 2026-09-08 22:46:24 UTC
TARGET_ORG not configured for obi; skipping public-org deep scan.
## REPOSCAN 2026-09-09 01:09:32 UTC
TARGET_ORG not configured for obi; skipping public-org deep scan.
## REPOSCAN 2026-09-09 06:08:34 UTC
TARGET_ORG not configured for obi; skipping public-org deep scan.
## REPOSCAN 2026-09-09 11:34:59 UTC
[HYP] No OBI-owned public GitHub repositories exist to audit
class: OTHER
asset: N/A
confidence: 100
reasoning: >
impact: N/A — no code to audit
verify_steps: >
TARGET_ORG not configured for obi; skipping public-org deep scan.
## REPOSCAN 2026-09-09 15:18:01 UTC
TARGET_ORG not configured for obi; skipping public-org deep scan.
## REPOSCAN 2026-09-09 18:44:58 UTC
TARGET_ORG not configured for obi; skipping public-org deep scan.
## REPOSCAN 2026-09-09 21:37:11 UTC
TARGET_ORG not configured for obi; skipping public-org deep scan.
## REPOSCAN 2026-09-09 23:31:47 UTC
TARGET_ORG not configured for obi; skipping public-org deep scan.
## REPOSCAN 2026-09-10 01:30:24 UTC
TARGET_ORG not configured for obi; skipping public-org deep scan.
## REPOSCAN 2026-09-10 06:39:21 UTC
TARGET_ORG not configured for obi; skipping public-org deep scan.
## REPOSCAN 2026-09-10 11:52:17 UTC
TARGET_ORG not configured for obi; skipping public-org deep scan.
## REPOSCAN 2026-09-10 16:13:05 UTC
TARGET_ORG not configured for obi; skipping public-org deep scan.
## REPOSCAN 2026-09-10 19:09:57 UTC
TARGET_ORG not configured for obi; skipping public-org deep scan.
## REPOSCAN 2026-09-10 21:42:50 UTC
TARGET_ORG not configured for obi; skipping public-org deep scan.
## REPOSCAN 2026-09-10 23:55:48 UTC
TARGET_ORG not configured for obi; skipping public-org deep scan.
## REPOSCAN 2026-09-11 04:16:03 UTC
TARGET_ORG not configured for obi; skipping public-org deep scan.
## REPOSCAN 2026-09-11 08:58:53 UTC
TARGET_ORG not configured for obi; skipping public-org deep scan.
## REPOSCAN 2026-09-11 13:30:22 UTC
TARGET_ORG not configured for obi; skipping public-org deep scan.
## REPOSCAN 2026-09-11 17:15:13 UTC
TARGET_ORG not configured for obi; skipping public-org deep scan.
## REPOSCAN 2026-09-11 19:51:01 UTC
[HYP] NO_CANDIDATES
class: OTHER
asset: N/A
confidence: 100
reasoning: The provided candidate list explicitly states "no org candidates"
impact: None
verify_steps: N/A - no repositories to audit
TARGET_ORG not configured for obi; skipping public-org deep scan.
## REPOSCAN 2026-09-11 22:25:38 UTC
TARGET_ORG not configured for obi; skipping public-org deep scan.
## REPOSCAN 2026-09-12 00:38:25 UTC
TARGET_ORG not configured for obi; skipping public-org deep scan.
## REPOSCAN 2026-09-12 05:05:52 UTC
TARGET_ORG not configured for obi; skipping public-org deep scan.
## REPOSCAN 2026-09-12 09:33:33 UTC
[HYP] Hardcoded admin identity in operations portal bootstrap
class: OTHER
asset: obi-services/obi-operations-portal/scripts/bootstrap-admin.ts:28
confidence: 70
reasoning: Hardcoded email michael.j@techguys.work and name "Michael J." in source code.
impact: Low — aids targeted phishing/social engineering against this admin
verify_steps: Confirm michael.j@techguys.work is an active OBI admin email
[HYP] Supabase open signup without email verification
class: MISCONFIG
asset: obi-services/obi-operations-portal/supabase/config.toml:176,226
confidence: 65
reasoning: enable_signup=true, enable_confirmations=false allows anyone to create accounts
impact: Medium — unauthorized account creation on operations portal
verify_steps: Attempt to register a new account on the deployed portal
[HYP] Weak password policy on operations portal
class: MISCONFIG
asset: obi-services/obi-operations-portal/supabase/config.toml:182-185
confidence: 80
reasoning: minimum_password_length=6, password_requirements="" (no complexity)
impact: Low — weak passwords increase account compromise risk
verify_steps: Attempt to set a weak password (e.g., "123456")
[HYP] MFA disabled on operations portal
class: MISCONFIG
asset: obi-services/obi-operations-portal/supabase/config.toml:302-308
confidence: 75
reasoning: TOTP and phone MFA both disabled (enroll_enabled=false, verify_enabled=false)
impact: Medium — privileged accounts lack MFA protection
verify_steps: Check if MFA enrollment is available in UI
[HYP] SSRF via x-forwarded-host in redirect builder
class: SSRF
asset: obi-services/obi-operations-portal/app/dashboard/clients/manage/route.ts:23-28
confidence: 35
reasoning: buildRedirectUrl uses request.headers.get("x-forwarded-host") without validation
impact: Low — potential redirect to attacker-controlled site
verify_steps: Test if x-forwarded-host header is trusted; attempt host injection
TARGET_ORG not configured for obi; skipping public-org deep scan.
## REPOSCAN 2026-09-12 13:15:32 UTC
[HYP] Open Redirect in Email OTP Confirmation Route
class: SSRF
asset: obi-services/obi-operations-portal/app/auth/confirm/route.ts:10,21
confidence: 85
reasoning: The GET handler reads `next` from the query string (line 10: `const next = searchParams.get("next") ?? "/"`) and passes it directly to `redirect(next)` (line 21) with zero validation. No allowlist check, no origin check. An attacker can craft /auth/confirm?token_hash=<valid>&type=magiclink&next=https://evil.example.com and, after the OTP is verified (or even on the error path at line 24 which also uses `redirect(/auth/error?error=${error?.message})` — though that one is hardcoded), redirect the victim post-auth. The error path redirect at line 24 interpolates `error?.message` into the redirect URL without encoding, enabling a secondary open-redirect via error message manipulation on the `/auth/error` path.
impact: High — post-authentication redirect to attacker-controlled domain can be chained with session token theft, phishing, or OAuth callback interception
verify_steps: 1. Visit https://obi-operations-portal.vercel.app/auth/confirm?token_hash=anything&type=magiclink&next=https://evil.example.com 2. If OTP verification fails, observe whether the error redirect respects the `next` param. 3. Confirm with a valid magiclink token that the post-confirmation redirect follows the attacker URL.
[HYP] x-forwarded-host Header Injection in Redirect Builders
class: SSRF
asset: obi-services/obi-operations-portal/app/dashboard/clients/manage/route.ts:19-36 (and 5 other route handlers)
confidence: 60
reasoning: The `buildRedirectUrl()` function in every management route handler reads `request.headers.get("x-forwarded-host")` and `request.headers.get("x-forwarded-proto")` to construct the redirect origin (e.g. line 23-28 of clients/manage/route.ts). If the Vercel edge or any upstream proxy passes untrusted x-forwarded-host through, an attacker can inject an arbitrary origin. The function falls back to `new URL(request.url).origin` only when host is null, but `x-forwarded-host` takes priority. This pattern is repeated in: clients/manage/route.ts, clients/projects/manage/route.ts, clients/assignments/manage/route.ts, users/status/route.ts, users/role/route.ts, users/invite/route.ts, users/invitation/route.ts.
impact: Medium — depends on whether Vercel's edge strips/overwrites x-forwarded-host; if not, enables open redirect after any successful POST action
verify_steps: 1. Send a POST to /dashboard/clients/manage with action=create and a valid session, setting header x-forwarded-host: evil.example.com. 2. Check if the 303 redirect Location header points to evil.example.com. 3. If Vercel strips it, this is mitigated.
[HYP] Hardcoded Admin Bootstrap Email Address
class: SECRET
asset: obi-services/obi-operations-portal/scripts/bootstrap-admin.ts:28-29
confidence: 90
reasoning: The bootstrap script hardcodes `email = "michael.j@techguys.work"` and `fullName = "Michael J."` with role "admin". This is the intended first admin account for the portal. The email domain `techguys.work` reveals the operational contractor/vendor identity and the admin invite target. Combined with the Supabase project ID "obi-operations-portal" visible in supabase/config.toml:5, this gives an attacker a confirmed admin email for password reset or social engineering.
impact: Medium — credential-stuffing/phishing target; not a direct vulnerability but significantly narrows attack surface for the bootstrap admin
verify_steps: 1. Confirm the repo is public on github.com/obi-services/obi-operations-portal. 2. Verify the email is still committed (it is, on main branch). 3. Check if michael.j@techguys.work has an active Supabase Auth account on the Vercel deployment.
[HYP] Weak Password Policy in Supabase Auth Configuration
class: MISCONFIG
asset: obi-services/obi-operations-portal/supabase/config.toml:182-185
confidence: 70
reasoning: The Supabase config sets `minimum_password_length = 6` and `password_requirements = ""` (empty string = no complexity requirements). While this is the local-dev config template, the fact that it ships committed to the public repo suggests it may mirror production defaults. Supabase Cloud projects inherit these settings unless explicitly overridden in the dashboard. A 6-character password with no complexity requirement is trivially brute-forceable.
impact: Medium — weak password policy on a portal managing OBI client credits, assignments, and user accounts
verify_steps: 1. Check the live Supabase project's Auth settings at the dashboard for minimum password length. 2. Attempt to sign up or change password with a 6-character all-lowercase password.
[HYP] Client Detail Page Uses Session Client for Data Fetch (RLS-Dependent IDOR Protection)
class: IDOR
asset: obi-services/obi-operations-portal/app/dashboard/clients/[clientCode]/page.tsx:128-136
confidence: 40
reasoning: The client detail page at `/dashboard/clients/[clientCode]` uses the session-scoped Supabase client (not the admin client) for the main data queries (lines 128-136, 148-154, 163-169). The page does call `requirePrivilegedPortalProfile()` which verifies admin/supervisor role, and the RLS policies restrict data access. However, the authorization depends entirely on Supabase RLS being correctly configured — if any RLS policy has a bug or is accidentally dropped during migration, the `clientCode` path parameter becomes a direct IDOR vector. The client_code values are sequential-ish (e.g. CL-001) making enumeration trivial.
impact: Low — RLS appears correctly configured, but the architecture creates a fragile single point of failure; any RLS migration mistake exposes all client data
verify_steps: 1. Check the RLS policies on the clients, projects, and project_assignments tables via the Supabase dashboard. 2. Confirm that `clients_select_authorized` policy correctly restricts non-privileged users.
TARGET_ORG not configured for obi; skipping public-org deep scan.
