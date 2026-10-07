# Management risk and remediation review

**Documentation review: October 7, 2026. Audience: security leadership, clinical operations, and the application owner.**

## Executive decision

The historical assessment reports 18 findings: seven closed and eleven open. All two critical and three high findings were reported closed. This review does not rerun application tests, examine a deployed environment, or approve use of real patient data. The recommended decision is to require current control evidence before any patient-data pilot approval.

The priorities below aggregate published finding categories. They are not newly discovered vulnerabilities, replacements for the original 18 finding IDs, or proof that every category remains deficient.

| Priority / review ID | Business exposure and reported condition | Proposed accountable role | Treatment and proposed milestone | Evidence required for closure |
|---|---|---|---|---|
| P1 / G01 — Authentication and authorization | Historical password-only acceptance undermined stated MFA; development routes reportedly needed server-side restrictions. Unauthorized access could expose sensitive records. | Application owner with IAM reviewer | Mitigate; reconcile fixes and negative tests before patient-data pilot review | Assessed commit, identity-provider policy, password-only/malformed-token rejection, restricted-route denial, dated results |
| P1 / G02 — Tenant separation and audit accountability | Published review describes audit coverage and isolation gaps. Cross-tenant access or unrecorded reads would impair confidentiality and investigation. | Application owner with database/security reviewer | Mitigate; verify isolation and audit behavior before pilot review | Two-tenant deny tests for reads, writes, files, and audit records; audit-write failure tests; restricted log access |
| P1 / G03 — Sensitive data outside primary storage | Historical concerns include logs, storage metadata, and notifications. Disclosure could reveal patient information or treatment affiliation. | Privacy lead with application owner | Mitigate; approve data-flow and disclosure rules before pilot review | Redacted data-flow inventory, log/notification samples, storage metadata review, applicable consent and vendor agreement review |
| P2 / G04 — Baseline hardening and maintenance | Historical categories include headers, rate limiting, errors, dependencies, and logging. Actual outstanding items require reconciliation. | Application owner | Mitigate; establish a dated backlog within 30 days of plan authorization | Original finding IDs, owners and dates; configuration evidence; dependency reports; negative tests and closure approvals |

P1 means decision gate; P2 means scheduled treatment. These are management priorities, not recalculated severity or likelihood scores. Roles and milestones are proposed, not assignments or completed commitments.

## Evidence and risk status

- **Confirmed documentation gap:** the public package lacks an assessed commit, dated regression output, and the full working findings log needed to reproduce closure.
- **Reported deficiency:** a condition described by the original author, without independent current verification in this pass.
- **Reported closed:** prior remediation claim; retain it without treating it as verified current effectiveness.
- **Inherent risk:** exposure before crediting safeguards. No quantitative inherent-risk scores were established in this pass.
- **Residual risk:** exposure after verified controls. Current residual risk is **unassessed**; no numerical reduction or “low risk” outcome is claimed.
- **Conditional residual risk:** an expected outcome if specified controls work; still requires validation and risk-owner acceptance.

The original findings total must remain 18 until the working log supports a reconciliation. Do not map these four review groups one-to-one to undisclosed findings or invent closure dates.

## Approval and exception rules

For each original finding, retain condition, criteria, cause (unknown unless evidenced), impact, recommendation, owner, target date, and closure artifact. Close only after a reviewer confirms the fix and relevant negative tests against a named version. A policy signature or code change alone is insufficient.

Before a real-patient-data pilot, require G01–G03 evidence, privacy applicability review, authorized data flows, operational incident response and recovery evidence, and written acceptance of remaining risks. Approval authority belongs to the accountable organization, not this portfolio. Any exception should record rationale, compensating controls, scope, expiry, and approving risk owner.

## Historical evidence

The [original assessment PDF](Security_Assessment_Portfolio_Summary.pdf) and existing graphics are historical artifacts. They are preserved, not reissued as current assurance. The [IAM matrix](IAM_Control_Review.md) defines the technical evidence needed for the management decision; the README retains the original case-study narrative.
