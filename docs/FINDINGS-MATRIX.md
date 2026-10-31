# Findings matrix — current reconciliation

> **Synthetic-data prototype.** No real patient data. Findings reflect a self-audit. This documentation does not establish production readiness or regulatory certification.

This is the per-finding status register for the MAT/MOUD self-assessment: the file that holds each finding's current status. The September baseline recorded 7 closed and 11 open. That baseline is preserved as history in the [README](../README.md) and the [management review](Management_Risk_Remediation.md); it is not restated here as current. What follows is anchored to a named application revision and a dated evidence record.

Status in this file reflects the application source, not a deployment. No reviewer has examined a running environment, and no operating evidence was collected in this pass.

## Evidence baseline

| Item | Value |
|---|---|
| Application revision assessed | `19603a2` (`main`, 2026-10-31) |
| Evidence record | `docs/evidence/CONTROL-TEST-RESULTS.json` at application revision `3c36593`, `base_revision` `3cdcaca` |
| API Jest suite | 87 passed, 7 skipped (opt-in PostgreSQL cases), 0 failed — 94 total; execution started `2026-10-31T03:56:18Z` |
| PostgreSQL isolation suite | 7 passed, 0 failed, disposable fixture dropped; execution started `2026-10-31T03:59:45Z` |
| Android JVM | 14 cases per variant, 0 failures/errors — **cached** (`UP-TO-DATE`) results from the 2026-10-30 build, reused because no Android input changed |
| Hosted CI | Run [41167003704](https://github.com/IAM-ZeroTrustRon/MATMOUD-clinical-compliance-suite/actions/runs/41167003704) on `3c36593` logged 87 passed / 7 skipped / 94 total and 7 passed; hosted results are cited from GitHub Actions, not imported into the evidence manifest |

**Revision relationship.** Test results recorded at `3c36593`; application code identical at `19603a2` (documentation-only difference); execution dates: 2026-10-31 UTC. Verified read-only: `git diff --name-status 3c36593 19603a2` reports one added documentation file and no change under `apps/`, `package.json`, or `package-lock.json`.

The distinction matters. A test result is evidence about the source bytes it ran against; the commit that records the result is a separate fact. Here the recording commit is `3c36593`, the tested source hashes are listed in the manifest, and the later commit `19603a2` changes documentation only.

The seven skipped API cases are the opt-in PostgreSQL tests. They are not passes in that run; they passed separately, in the suite row above.

## Current totals

**18 findings: 13 closed, 1 partial, 4 open.**

The 13 closed findings are **not** "13 fully tested closures." They carry three different evidence strengths:

| Evidence group | Finding IDs | Count |
|---|---|---:|
| Closed with tests | MOUD-01, 03, 04, 05, 07, 09, 11, 12, 13 | 9 |
| Closed by structural check | MOUD-14, 15, 18 | 3 |
| Closed by inspection; regression test pending | MOUD-02 | 1 |
| Partial | MOUD-08 | 1 |
| Open | MOUD-06, 10, 16, 17 | 4 |

| Severity | Closed | Partial | Open | Total |
|---|---:|---:|---:|---:|
| Critical | 2 | 0 | 0 | 2 |
| High | 3 | 0 | 0 | 3 |
| Medium | 3 | 1 | 2 | 6 |
| Low | 5 | 0 | 2 | 7 |
| **Total** | **13** | **1** | **4** | **18** |

The Medium closed findings are MOUD-07, MOUD-09, and MOUD-11; the partial is MOUD-08; the open are MOUD-06 and MOUD-10. MOUD-11 originally carried a mixed "Low–Medium" label while sitting inside the Medium section of the original assessment. That established Medium bucket is retained here so the totals reconcile against the original document rather than against a re-scoring.

## How to read evidence strength

- **Test-backed, reversion reported, not re-executed** — a regression test exists, and the prior assessment reports that restoring the defective code made it fail. The reversion was **not** repeated in this documentation pass. The claim is reported, not reproduced here.
- **Test-backed, reversion not demonstrated** — the test exists and passed in the recorded run, but no one has shown it fails without the fix. A passing test supports only its stated assertions.
- **Structural verification** — the control was confirmed by inspecting repository structure rather than by executing a test.
- **Inspection only** — the fix was confirmed by reading the implementation. No automated test asserts it.
- **Open** — no remediation is claimed.

## Matrix

Open findings are published as **ID and category only**. File paths, line numbers, code excerpts, and exploitation conditions are deliberately omitted from this public document; omission is not a substitute for access control, and it is not a claim that the detail does not exist.

| ID | Severity | Category | Status | Evidence strength | Application implementation | Verification reference | Reversion | Remaining limitation / follow-up |
|---|---|---|---|---|---|---|---|---|
| MOUD-01 | Critical | Authentication — MFA policy enforcement | Closed | Test-backed; reversion reported, not re-executed | `apps/api/src/middleware/auth.middleware.ts` | `apps/api/src/middleware/__tests__/auth.middleware.test.ts` (MFA suite, 5 cases) | Reversion result reported, not re-executed (2 failures) | Identity-provider factor configuration and device evidence not reviewed; unit tests mock JWT verification and JWKS |
| MOUD-02 | Critical | Development route exposure | Closed by inspection; regression test pending | Inspection only | `apps/api/src/routes/credentials.routes.ts` (registration-time environment guard, tier requirement, mutation audit) | No automated test; `grep` over `apps/api/test` returns zero references | Not demonstrated — no test exists | Closure rests on reading a registration-time guard; no test asserts the production 404 |
| MOUD-03 | High | Document retrieval — link lifetime | Closed | Test-backed; reversion reported, not re-executed | `apps/api/src/config/s3.ts` (300-second TTL constant) | `apps/api/src/config/__tests__/s3.test.ts` (5 cases, including bounded-expiry assertion) | Reversion result reported, not re-executed (expiry assertion failed at the former 7-day value) | Signing is mocked; actual cloud-side expiry and link sharing are not validated |
| MOUD-04 | High | Upload — content-based type validation | Closed | Test-backed; reversion reported, not re-executed | `apps/api/src/utils/validator.ts` | `apps/api/src/utils/__tests__/validator.test.ts` (18 executed cases) plus the MIME-mismatch route case | Reversion result reported, not re-executed (3 failures) | Signature allowlist only; not malware scanning or full document parsing |
| MOUD-05 | High | Storage-key construction | Closed | Test-backed; reversion not demonstrated | `apps/api/src/routes/credentials.routes.ts` (generated key: tenant, credential, random identifier, extension from detected type) | `apps/api/test/credentials.routes.test.ts` (ownership/commit/retrieval case) and validator extension tests | Test exists; reversion not demonstrated | No reversion of the original filename-in-key defect is recorded; cloud metadata/logging policy unverified |
| MOUD-06 | Medium | Audit-trail tenant isolation | Open | — | Open; technical detail omitted from public reconciliation | Open; technical detail omitted from public reconciliation | — | Tracked as open; follow-up requires a tenant-scoped audit design, not broader grants |
| MOUD-07 | Medium | Sensitive-read auditing | Closed | Test-backed; reversion not demonstrated | `apps/api/src/routes/credentials.routes.ts`, `apps/api/src/middleware/audit.middleware.ts` | `apps/api/test/credentials.routes.test.ts` (audit failure yields 500 with no signing; list reads yield no records) and `credentials.postgres.test.ts` (revoked INSERT) | Test exists; reversion not demonstrated | Ordering is proven with a database double and a revoke; no reversion recorded. Mutation audit atomicity remains a separate open item (see the five gates) |
| MOUD-08 | Medium | Security regression coverage across required paths | Partial | Test-backed in part | Tests listed in the partial section below | 4 of 5 acceptance requirements met; RBAC boundary coverage incomplete | Reversion demonstrated for none of the newly cited suites | Suite size does not close this finding; the uncovered requirement has no dedicated cases |
| MOUD-09 | Medium | Notification payload — PHI fields | Closed | Test-backed; reversion not demonstrated | `apps/api/src/services/email.service.ts` (template field allowlist) | `apps/api/test/email.service.test.ts` (2 cases, including a blocked-field sentinel) | Test exists; reversion not demonstrated | Weak reversion posture: re-adding the field to the type would not fail the test; only populating it does |
| MOUD-10 | Medium | Dependency/workspace placement | Open | — | Open; technical detail omitted from public reconciliation | Open; technical detail omitted from public reconciliation | — | Tracked as open; the package remains declared in the wrong workspace |
| MOUD-11 | Medium | Edge hardening — headers, origin policy, rate limits | Closed | Test-backed; reversion not demonstrated | `apps/api/src/server.ts` | `apps/api/test/http-hardening.test.ts` (5 cases) and `apps/api/test/rate-limit.test.ts` (4 cases) | Test exists; reversion not demonstrated | Limiter state is in-process, so it is not shared across replicas; thresholds are configuration, not proof of capacity |
| MOUD-12 | Low | Health-check information disclosure | Closed | Test-backed; reversion not demonstrated | `apps/api/src/server.ts` (`/health` returns status, dependency state, timestamp only) | `apps/api/test/credentials.routes.test.ts` — degraded health result without details, connection released | Test exists; reversion not demonstrated | Liveness semantics only; no uptime monitoring or alerting evidence |
| MOUD-13 | Low | Error-message hygiene | Closed | Test-backed; reversion not demonstrated | `apps/api/src/middleware/http.middleware.ts` (fixed bodies; identical in development) | `apps/api/test/credentials.routes.test.ts` (parser-error sanitisation) and `http-hardening.test.ts` (no-store on error responses) | Test exists; reversion not demonstrated | Driver failures collapse to a generic body by design, which limits field-level diagnosis |
| MOUD-14 | Low | Unused dependency | Closed | Structural verification | Absent from the root and API manifests | Search across every tracked `package.json`; no reference found | Structural verification | Covers tracked manifests only; nothing outside the repository was inspected |
| MOUD-15 | Low | Repository line-ending control | Closed | Structural verification | `.gitattributes` at the repository root: `* text=auto eol=lf` plus binary rules | Tracked file content at the assessed revision | Structural verification | Applies to tracked text; it does not repair line endings already stored in older blobs |
| MOUD-16 | Low | Route/service separation | Open | — | Open; technical detail omitted from public reconciliation | Open; technical detail omitted from public reconciliation | — | Tracked as open |
| MOUD-17 | Low | Logging structure | Open | — | Open; technical detail omitted from public reconciliation | Open; technical detail omitted from public reconciliation | — | Tracked as open |
| MOUD-18 | Low | Nested repository | Closed | Structural verification | Active workspace filesystem inspection found only the root repository metadata; no nested repository directory is present | `git ls-files -s` reports no mode-`160000` gitlinks (no tracked submodules) | Structural verification | Filesystem inspection is scoped to this working tree at this revision. A tracked-tree check alone cannot prove that ignored nested repositories are absent in other checkouts, clones, or machines |

## Closed with tests

MOUD-01, 03, 04, 05, 07, 09, 11, 12, and 13 are closed with regression tests recorded in the evidence baseline. For MOUD-01, 03, and 04 the prior assessment reports that restoring the original defective code made the tests fail; that reversion result is reported, not re-executed in this pass. The other six have tests without a demonstrated reversion, which is a weaker claim: the test proves the current behavior, not that it would catch a regression of the original defect.

## Closed by structural check

MOUD-14, 15, and 18 were verified by inspection of repository structure.

- **MOUD-14** — the dependency is absent from every tracked manifest. That is a tracked-file check, not a runtime check.
- **MOUD-15** — the repository declares line-ending normalization and binary exceptions in `.gitattributes`. The control governs future checkouts; it does not rewrite blobs already committed.
- **MOUD-18** — two different checks are relevant and they are not interchangeable. A filesystem inspection of the active workspace confirmed no nested repository inside the working tree, and a tracked-index inspection confirmed no submodule gitlinks. The tracked-tree check alone cannot establish that an ignored nested repository is absent, because an ignored directory does not appear in either the index or a `git ls-files` listing.

## Closed by inspection, regression test pending

**MOUD-02** — a development-only route is excluded from production registration at module load, and in non-production it additionally requires a tier and runs through the mutation audit middleware. Closure is supported by reading that guard. No test asserts the production absence, so no test would fail if the guard were removed. It is listed separately from the tested closures for exactly that reason.

## Partial

**MOUD-08** remains **partial** against its original acceptance scope. Tests cover several required behaviors; complete RBAC boundary coverage is incomplete, and suite totals do not establish the finding's closure.

| Original acceptance requirement | Verification reference | Coverage |
|---|---|---|
| Reject a password-only login | `auth.middleware.test.ts` MFA suite | Met |
| Tenant A cannot read tenant B's rows | `credentials.postgres.test.ts` (unfiltered select under a restricted role); `credentials.routes.test.ts` (unowned identifier) | Met |
| Each RBAC tier boundary enforced server-side | `credentials.routes.test.ts` (restricted tier denial); `alerts.routes.test.ts` (viewer denial) | **Partial — two role boundaries are exercised; the role-to-tier mapping and the highest boundary have no dedicated cases** |
| PHI masking for the limited tier | `credentials.routes.test.ts` (masking with an unchanged boolean contract) | Met |
| Upload type rejection | `validator.test.ts`; `credentials.routes.test.ts` (MIME mismatch) | Met |

The uncovered requirement is a coverage gap, not a demonstrated defect. The specific uncovered boundary is not enumerated in this public document.

## Open

Published as ID and category only:

| ID | Category |
|---|---|
| MOUD-06 | Audit-trail tenant isolation |
| MOUD-10 | Dependency/workspace placement |
| MOUD-16 | Route/service separation |
| MOUD-17 | Logging structure |

## Secret-scanning status

Provider-pattern alerts: 0; full content scan pending. The zero count is a provider-detection alert result reported on 2026-10-31 and not re-queried in this documentation pass. Provider-pattern alert results do not establish the absence of secrets throughout all repository content and history.

Historical development credential-like string reported; validity unconfirmed; rotation decision pending. No secret value is reproduced in this document.

## Provenance limitations

- **No application test was executed in this documentation phase.** The figures above are read from the committed evidence record and the cited hosted run.
- **One component is cached.** The Android row is a cached attestation from the 2026-10-30 build. Hosted CI re-executes it; that hosted result is cited separately and is not part of the evidence manifest.
- **No deployment was reviewed.** Nothing here evidences production configuration, runtime database role, secret management, backup, or restore behavior.
- **Skipped is not passed.** The seven opt-in PostgreSQL cases are skipped in the default API run and passed in their own run; both facts are stated.
- **Reversion claims are attributed.** Where a reversion result is reported rather than reproduced, the row says so. The prior assessment material that records it is not published as a tracked file in this repository.
- **Closure language.** Closed items read as remediated and verified at revision `19603a2` on 2026-10-31 **to the strength stated in their row**. This matrix is not a security, compliance, certification, or production-readiness finding. Five gates remain open in the application handoff: least-privilege runtime database role, encryption of stored sensitive fields, atomic mutation auditing, CEU referential integrity, and signed business associate agreements.

## Related documents

- [README](../README.md) — case study, methodology, and the preserved historical baseline.
- [Management risk and remediation review](Management_Risk_Remediation.md) — priorities, accountable roles, and closure requirements.
- [IAM control and evidence matrix](IAM_Control_Review.md) — control-level assurance, including the gaps that outlive finding closure.
- [Audit methodology](audit-methodology.mmd) — the five-phase approach this reconciliation follows.
