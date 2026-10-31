# IAM control and evidence matrix

**Scope:** review objectives for the MOUD application assessment. No application changes or new runtime tests were performed in this documentation pass. Statuses below are anchored to application revision `19603a2` and the 2026-10-31 evidence record; per-finding detail is in the [findings matrix](FINDINGS-MATRIX.md).

**Current totals across the 18 findings: 13 closed — nine test-backed, three structural, one inspection-only — 1 partial, and 4 open.** Control status below is not a re-scoring of those findings; a control can be partial while several of its findings are closed.

> Synthetic-data prototype. No real patient data. Findings reflect a self-audit. This documentation does not establish production readiness or regulatory certification.

| Control | Objective and business relevance | Evidence to request / negative test | Current assurance status | Related review / framework objective |
|---|---|---|---|---|
| IAM01 — Authentication strength | Enforce the declared MFA policy; password-only access must not satisfy it | Provider-specific claim documentation, policy export, dated password-only and malformed-claim rejection tests; valid allowed-session test | **Remediated and verified at `19603a2` on 2026-10-31 for claim-policy behavior** (unit tests for password-only, ambiguous, absent, and empty claims). Real RS256 signature and JWKS integration assurance **remains open**: the suite mocks both verification and key retrieval | G01; HIPAA 164.312(d); NIST CSF 2.0 PR.AA-03 |
| IAM02 — Server authorization | Enforce permitted actions at the server rather than relying on interface restrictions | Role/action matrix; direct-request deny tests; development routes excluded or restricted by environment | **Remediated and verified at `19603a2` on 2026-10-31** for route-level tier denial and rejection before storage. One development-route registration guard rests on **inspection only**; its regression test is pending | G01; HIPAA 164.312(a)(1); PR.AA-05 |
| IAM03 — Tenant boundaries | Prevent one clinic from accessing another clinic's records, files, or audit events | Tenant-context design; two-tenant read/write/file tests including pooled connections and failure paths | **Partial (MOUD-08)** — verified at `19603a2` on 2026-10-31 for unfiltered cross-tenant reads under a restricted role and for denial before storage. Complete RBAC boundary coverage and the runtime role posture remain unproven; do not infer effectiveness from the presence of row-level security | G02; HIPAA 164.312(a)(1); PR.AA-05 |
| IAM04 — Account lifecycle | Remove or adjust access when staff leave or change responsibilities | Joiner/mover/leaver procedure; termination and role-change samples; periodic access review with recorded decisions | **Evidence gap, unchanged** — no lifecycle artifacts were reviewed in this pass. Absence of evidence is not a confirmed implementation defect | G01; HIPAA 164.308(a)(3), 164.308(a)(4); PR.AA-01, PR.AA-05 |
| IAM05 — Audit accountability | Attribute sensitive access and administrative actions, preserving tenant boundaries | Redacted events for reads, exports, role changes; retention/access policy; audit-write failure and tamper-resistance tests | **Partial** — fail-closed sensitive-read auditing verified at `19603a2` on 2026-10-31 (audit write precedes release, including a database-permission revoke case). Mutation auditing remains asynchronous rather than atomic, and audit-trail tenant isolation (MOUD-06) remains open | G02; HIPAA 164.312(b); PR.PS-04 |
| IAM06 — File access and sensitive disclosure | Limit document release and avoid sensitive information leaking through metadata or notifications | URL lifetime and authorization tests; redacted notification/log samples; storage access policy; privacy review | **Partial** — bounded link lifetime, non-identifying storage keys, and a notification field allowlist verified at `19603a2` on 2026-10-31. Cloud-side expiry, bucket policy, and key lifecycle were not validated by those tests | G03; HIPAA 164.312(a)(1), 164.312(e)(1); PR.AA-05, PR.DS-02 |

Mappings indicate relevant objectives, not proven compliance or a legal finding. Part 2 protections depend on actual program and record applicability, consent, and disclosure circumstances; IAM alone does not establish Part 2 compliance.

A single finding's closure never closes a control family. IAM-01 is the clearest case: claim-policy behavior is test-backed, while signature verification and key retrieval assurance stays open. Read a control's status as its weakest supported claim, not its strongest.

## Evidence acceptance

Record artifact ID, original finding ID, assessed commit/environment, collection date, expected result, actual result, reviewer, and decision. Redact patient information, tokens, secrets, and unnecessary operational details before publishing. Store sensitive validation artifacts in the appropriate restricted evidence repository; public summaries can link to sanitized evidence descriptions.

Authentication-method labels must be evaluated using the identity provider's semantics and policy. A token claim or allow-list by itself does not establish phishing resistance, freshness, or enforcement across all access paths. The existing MFA tests mock JWT verification and JWKS retrieval, so they evidence claim handling only — not real cryptographic integration, and not production enforcement.

## Framework references

- [HHS Security Rule summary](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html)
- [HHS Part 2 guidance](https://www.hhs.gov/hipaa/part-2/index.html)
- [NIST CSF 2.0](https://doi.org/10.6028/NIST.CSWP.29)

Use the [management review](Management_Risk_Remediation.md) for priorities and approval conditions rather than duplicating its risk narrative here.
