# IAM control and evidence matrix

**Scope:** review objectives for the MOUD application assessment. No application changes or new runtime tests were performed in this documentation pass.

| Control | Objective and business relevance | Evidence to request / negative test | Current assurance status | Related review / framework objective |
|---|---|---|---|---|
| IAM01 — Authentication strength | Enforce the declared MFA policy; password-only access must not satisfy it | Provider-specific claim documentation, policy export, dated password-only and malformed-claim rejection tests; valid allowed-session test | Historical fix reported; current effectiveness pending | G01; HIPAA 164.312(d); NIST CSF 2.0 PR.AA-03 |
| IAM02 — Server authorization | Enforce permitted actions at the server rather than relying on interface restrictions | Role/action matrix; direct-request deny tests; development routes excluded or restricted by environment | Historical remediation reported; current effectiveness pending | G01; HIPAA 164.312(a)(1); PR.AA-05 |
| IAM03 — Tenant boundaries | Prevent one clinic from accessing another clinic's records, files, or audit events | Tenant-context design; two-tenant read/write/file tests including pooled connections and failure paths | Review required; do not infer effectiveness from presence of row-level security | G02; HIPAA 164.312(a)(1); PR.AA-05 |
| IAM04 — Account lifecycle | Remove or adjust access when staff leave or change responsibilities | Joiner/mover/leaver procedure; termination and role-change samples; periodic access review with recorded decisions | Evidence gap; absence of evidence is not a confirmed implementation defect | G01; HIPAA 164.308(a)(3), 164.308(a)(4); PR.AA-01, PR.AA-05 |
| IAM05 — Audit accountability | Attribute sensitive access and administrative actions, preserving tenant boundaries | Redacted events for reads, exports, role changes; retention/access policy; audit-write failure and tamper-resistance tests | Historical gaps reported; current coverage pending | G02; HIPAA 164.312(b); PR.PS-04 |
| IAM06 — File access and sensitive disclosure | Limit document release and avoid sensitive information leaking through metadata or notifications | URL lifetime and authorization tests; redacted notification/log samples; storage access policy; privacy review | Historical fixes and concerns reported; remaining scope requires reconciliation | G03; HIPAA 164.312(a)(1), 164.312(e)(1); PR.AA-05, PR.DS-02 |

Mappings indicate relevant objectives, not proven compliance or a legal finding. Part 2 protections depend on actual program and record applicability, consent, and disclosure circumstances; IAM alone does not establish Part 2 compliance.

## Evidence acceptance

Record artifact ID, original finding ID, assessed commit/environment, collection date, expected result, actual result, reviewer, and decision. Redact patient information, tokens, secrets, and unnecessary operational details before publishing. Store sensitive validation artifacts in the appropriate restricted evidence repository; public summaries can link to sanitized evidence descriptions.

Authentication-method labels must be evaluated using the identity provider's semantics and policy. A token claim or allow-list by itself does not establish phishing resistance, freshness, or enforcement across all access paths.

## Framework references

- [HHS Security Rule summary](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html)
- [HHS Part 2 guidance](https://www.hhs.gov/hipaa/part-2/index.html)
- [NIST CSF 2.0](https://doi.org/10.6028/NIST.CSWP.29)

Use the [management review](Management_Risk_Remediation.md) for priorities and approval conditions rather than duplicating its risk narrative here.
