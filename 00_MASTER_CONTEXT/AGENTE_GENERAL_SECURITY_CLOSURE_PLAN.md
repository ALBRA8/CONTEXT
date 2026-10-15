# AGENTE-GENERAL — Security implementation and closure plan

**Date:** 2026-10-14  
**Status:** IMPLEMENTATION REQUIRED — NOT A PRODUCTION SECURITY CERTIFICATION  
**Scope:** Canonical acceptance contract for the security work on AGENTE-GENERAL. This file records requirements and evidence; it does not claim that the application code has implemented or passed them.

## Authority and change boundary

Under the CONTEXT bootstrap rules, this repository is the only writable repository for this workflow. AGENTE-GENERAL is a read-only evidence source here. Do not edit its branches, merge pull requests, deploy, or change runtime secrets from this workflow. Code implementation must be performed by an explicitly authorized workflow whose write boundary permits AGENTE-GENERAL.

## Release-blocking work packages

### SEC-01 — Provider-settings API authentication

**Required behavior**
- Require server-side authentication on every endpoint that reads or mutates provider configuration, API keys, defaults, tests, or reset state. Public health/readiness endpoints must be explicitly enumerated and must not expose configuration.
- Fail closed if the expected server token is missing, malformed, or configured insecurely. Compare secrets using constant-time comparison; never log tokens or request authorization headers.
- Test missing, invalid, valid, empty, whitespace, malformed, and rotated tokens. Assert unauthorized requests make no database or provider changes.
- Ensure the browser sends the token only to the configured trusted API origin. Never place credentials in `NEXT_PUBLIC_*`, localStorage, sessionStorage, URLs, analytics, or client logs. Prefer a same-origin backend/BFF or a deployment-controlled secret injection path; a token shipped to public browser JavaScript is not a secret.
- Apply restrictive CORS, host/origin validation, HTTPS in deployed environments, and rate limits to provider-test and other potentially costly endpoints.

**Evidence to attach:** test run URL/commit, negative/positive auth test output, browser network inspection, production-build secret scan, and configuration example with values redacted.

### SEC-02 — Provider credential storage and cryptographic lifecycle

- Keep provider API keys encrypted at rest with a separately managed `SETTINGS_ENCRYPTION_KEY`; never generate a new key implicitly on startup.
- Fail closed for secret reads/writes if the key is missing, invalid, or cannot decrypt existing ciphertext. Errors must not include key material or ciphertext.
- Define key rotation with an explicit migration procedure: decrypt using the old key in a controlled process, re-encrypt with the new key, verify record counts and decryptability, then retire the old key. Document recovery and backup requirements; do not silently reset credentials.
- Ensure database, parent directory, WAL/SHM files, and backups have restrictive ownership/permissions. Use a private directory not writable by untrusted users. Symlink rejection must be combined with safe directory permissions and race-resistant file handling where supported.
- Validate schema versions and database integrity; handle corrupt DB and wrong-key cases without leaking secrets or silently overwriting data.
- Ensure non-sensitive configuration responses return only an explicit allowlist and an `api_key_set` boolean, never the key or encrypted value.

**Evidence to attach:** tests for missing/wrong/rotated key, round-trip encryption, redacted response/log checks, file-permission checks, corrupt DB handling, and isolated backup/restore drill.

### SEC-03 — Dashboard/browser boundary

- Build the dashboard with production settings and inspect generated JS, source maps, static assets, logs, and browser storage for credentials.
- Do not embed a reusable administrative bearer token in a public client bundle. If a token is required, route through a same-origin authenticated backend/BFF or another server-side session design. If the current UI obtains a token in memory, document its threat model and prove the token is not a build-time public secret.
- Restrict API base URL to an operator-controlled trusted origin; reject unexpected origins and insecure HTTP outside local development.
- Add safe loading/error states that never render response bodies containing secrets. Never prefill a stored API key; clear password input after successful save and on navigation where practical.
- Check dependency audit, CSP, clickjacking protection, secure transport, and production source-map policy.

**Evidence to attach:** exact build command and output, dependency audit, bundle scan, browser devtools verification, and deployed origin/CSP headers.

### SEC-04 — Provider adapter and outbound network

- Validate provider ID, model, URL scheme, host, numeric ranges, timeout, and token limits before saving or making requests.
- Prevent SSRF: do not allow arbitrary user-controlled base URLs to reach loopback, link-local, private, metadata, or internal control-plane addresses unless explicitly allowlisted for an operator-managed deployment. Revalidate after DNS resolution and redirects; disable redirects or validate every hop.
- Set bounded connect/read/overall timeouts and response-size limits. Retry only idempotent/transient operations with bounded exponential backoff and jitter; never retry authentication failures or non-idempotent side effects blindly.
- Redact authorization headers, API keys, prompt content where sensitive, and provider response bodies from logs/errors. Keep health checks minimal and distinguish configured, authenticated, and reachable states.
- Ensure streaming/tool-call capabilities are declared accurately; reject unsupported combinations instead of silently degrading.

**Evidence to attach:** URL/SSRF negative tests, timeout/oversized-response tests, retry matrix, redaction tests, and mocked provider contract tests. Real-provider smoke tests must use approved test credentials in staging only.

### SEC-05 — Toolkit and planner execution contracts

- Validate tool arguments against the registered JSON Schema before invocation and validate/normalize outputs against the declared output contract.
- Enforce authorization and policy checks at the actual dispatch boundary, not only in planner/UI metadata. Default deny unknown tools, capabilities, principals, and effects.
- Enforce per-run deadline, per-tool timeout, maximum tool calls, retry budget, concurrency, output bytes/tokens, and total resource budget in executable code. Counters must be incremented at the actual execution boundary.
- Prevent retry loops and recursive delegation from bypassing limits. Cancellation must propagate to child tasks and provider requests.
- Separate read-only from side-effecting tools. Side effects require idempotency keys or explicit HITL approval as applicable; do not replay an uncertain dispatch automatically.
- Bound tool output, sanitize untrusted output before presenting it as instructions, and treat provider/tool output as data rather than trusted policy.

**Evidence to attach:** schema rejection tests, authorization matrix, timeout/cancellation tests, budget boundary tests, malformed-output tests, and duplicate/ambiguous-dispatch tests.

### SEC-06 — HITL approvals, durable state, and crash recovery

- Bind each approval to the exact principal, execution/correlation ID, tool-call ID/name, canonical argument digest, policy version, and deadline.
- Enforce expiry at decision and dispatch time. Approval/rejection must be atomic, conflict-safe, and one-shot.
- Persist only the minimum necessary state; never persist bearer credentials or raw sensitive tool arguments unless explicitly justified and protected.
- On crash or ambiguous external effect, mark outcome unknown and require reconciliation. Do not claim exactly-once effects unless the downstream system supports idempotency/transactions.
- Document that SQLite coordination is limited to workers sharing a supported local filesystem; do not claim cross-host coordination from SQLite alone.
- State clearly that persistence of an approval record does not reconstruct a lost live coroutine/provider state. Fail closed rather than recreating or replaying execution state without a supported recovery contract.

**Evidence to attach:** expiry races, concurrent decisions, duplicate claim, restart, multi-process, corruption, binding mismatch, and simulated crash-after-side-effect tests.

### SEC-07 — Runtime, deployment, and operations

- In staging, verify startup/shutdown lifecycle, agent registration, health/readiness semantics, and a complete authorized execution path. An HTTP 200 alone is not readiness evidence.
- Keep public execution default-deny. Require explicit authentication, authorization, quotas, and network controls for any execution endpoint.
- Pin or lock deployable dependency resolution; audit Python core/dev/API/A2A/crypto/provider extras and dashboard npm dependencies separately.
- Scan tracked files, history where appropriate, build artifacts, logs, and deployment configuration for secrets. Rotate any credential found in history or artifacts.
- Exercise backup/restore, encryption-key recovery, rollback, incident response, and audit-log preservation. Define RPO/RTO with the operator rather than treating proposals as accepted targets.
- Require independent reviewer sign-off and a dated evidence bundle tied to immutable commit SHAs and deployment image digest.

**Evidence to attach:** staging URL/environment ID (no secrets), image digest, deployment logs, health/readiness results, acceptance matrix, backup/restore evidence, dependency reports, and signed approval.

## Pull request integration order

1. Review the base/head ancestry and changed-file overlap for provider/security work (currently PR #46 and #48); integrate on an authorized branch and rerun all checks on the final merge candidate.
2. Validate runtime bootstrap/deployment work (PR #45) independently in staging; do not infer readiness from source changes alone.
3. Review toolkit/planner contracts in PR #49 and test enforcement at dispatch, not merely declaration.
4. Keep HITL durable-recovery work (PR #3) unmerged as a complete production guarantee until its documented process-death and external-side-effect limits are either addressed or explicitly accepted as product constraints.
5. Evaluate CI dependency updates (#42/#43) with the resulting workflow runs and permissions review.
6. Complete staging acceptance/backup evidence (#41). Its described test-database drill is not a production backup/restore test.

## Minimum release gate

Do not label the agent production-ready or frozen until all of the following are true:

- [ ] Every release-blocking test above has reproducible passing evidence tied to the final candidate commit.
- [ ] Python test suite, clean-room checks, audit/parity/contamination gates pass on the integrated candidate.
- [ ] Dashboard production build and npm dependency audit pass; no public bundle contains reusable secrets.
- [ ] API auth and provider-settings negative tests pass; unauthorized requests have no side effects.
- [ ] Encryption lifecycle, wrong/missing-key behavior, permissions, and backup/restore are tested.
- [ ] Toolkit schema validation, dispatch authorization, timeouts, budgets, cancellation, and retry behavior are enforced and tested.
- [ ] HITL expiry, duplicate claims, ambiguous dispatch, and crash behavior meet the documented fail-closed contract.
- [ ] Staging deployment and acceptance matrix are complete; all blocked/not-run items are resolved or explicitly risk-accepted by the accountable owner.
- [ ] Independent security review and operational owner sign-off are recorded against immutable commit/image identifiers.
- [ ] `docs/CERTIFICATION.md`, CONTEXT `CURRENT_STATE.md`, `KNOWLEDGE_INDEX.md`, and `CHANGELOG.md` accurately reflect evidence and remaining exceptions.

## Current audit conclusion

Based on the evidence reviewed on 2026-10-14, AGENTE-GENERAL is **NOT READY FOR FINAL FREEZE / PRODUCTION NOT VERIFIED**. The documented 648-test CI result applies to commit `4c838850161f9c876dbdf084833a02dca6ed5806`; it does not certify later open pull requests or a real deployment. This plan is a requirements/acceptance contract, not evidence that the listed controls have been implemented or passed.
