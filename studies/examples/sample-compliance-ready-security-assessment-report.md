# Sample Compliance-Ready Security Assessment Report

This sanitized sample shows the shape of a Vantix compliance-ready report. It uses fictional findings and no real customer data.

## Executive Summary

Vantix validated three technical security findings and recorded one source-backed candidate that requires runtime confirmation. The strongest risks affect access control, sensitive data protection, and vulnerability management. Evidence is suitable for engineering remediation and compliance evidence review after customer-specific scope and legal obligations are confirmed.

## Compliance Readiness Summary

| Framework | Evidence status | Notes |
|---|---|---|
| SOC 2 | Mapped evidence | Supports access-control and vulnerability-management evidence. |
| ISO/IEC 27001 | Mapped evidence | Supports access-control and secure-development evidence. |
| HIPAA Security Rule | Conditional mapped evidence | Relevant only when the assessed system processes or protects ePHI. |
| GDPR | Conditional mapped evidence | Relevant when personal data processing is in scope. |
| NIST CSF | Mapped evidence | Supports Protect, Identify, Detect, and Respond evidence areas. |

## Control Mapping Matrix

| Finding ID | Evidence ID | Risk area | Control family | Framework mappings | Proof status |
|---|---|---|---|---|---|
| F-001 | E-001 | Access control | Access Control | SOC 2, ISO/IEC 27001, HIPAA, GDPR, NIST CSF | Replayable proof pack |
| F-002 | E-002 | Data protection | Security of Processing | GDPR, HIPAA, CCPA/CPRA, CSA CCM | Validated proof |
| F-003 | E-003 | Vulnerability management | Secure Development | ISO/IEC 27001, CIS Controls, NIST SSDF, PCI DSS | Runtime evidence |
| C-001 | E-004 | Secure development | Source candidate | ISO/IEC 27001, NIST SSDF | Needs runtime validation |

## Validated Findings

### F-001 Broken object-level authorization

- Severity: High
- Evidence quality: 4, replayable proof pack
- Business impact: Unauthorized access to another controlled user's object.
- Compliance impact: Supports access-control testing evidence across SOC 2, ISO/IEC 27001, HIPAA technical safeguards when ePHI is in scope, GDPR security of processing when personal data is in scope, and NIST CSF Protect.
- Remediation: Enforce server-side object ownership checks and add regression tests for cross-user access attempts.

### F-002 Sensitive data exposed in API response

- Severity: High
- Evidence quality: 3, validated proof
- Business impact: Excess data disclosure to authenticated users without a need to know.
- Compliance impact: Supports data-protection and security-of-processing evidence.
- Remediation: Minimize response fields, apply authorization-aware serializers, and add test coverage for sensitive fields.

### F-003 Vulnerable dependency with reachable route

- Severity: Medium
- Evidence quality: 2, runtime evidence
- Business impact: A reachable route uses a component version with public exploit history.
- Compliance impact: Supports vulnerability-management and secure-development evidence.
- Remediation: Upgrade the dependency and rerun validation.

## Source-Backed Candidate

### C-001 Missing authorization middleware on administrative route

- Status: Candidate only
- Evidence quality: 1, source/scanner evidence
- Boundary: Not reportable as a validated finding until runtime proof confirms reachability and impact.

## Negative Evidence

- XSS probes did not produce executable script in the tested forms.
- Repeated API mutation attempts returned identical authorization failures after session preservation.

## Audit Evidence Appendix

- Proof pack links: `proof-pack/F-001/`
- Replay metadata: `proof-pack/F-001/validation/replay.json`
- Artifact hashes: `proof-pack/F-001/hashes.json`
- Redaction manifest: `report-pack/compliance_summary/redaction_manifest.json`
- Timeline: `artifacts/timeline.csv`
- Screenshots: redacted browser screenshots linked from the artifact index.

## Remediation Tracker

| Finding | Owner | SLA | Fix guidance | Verification step | Framework impact |
|---|---|---|---|---|---|
| F-001 | Engineering | 14 days | Add server-side authorization checks. | Replay proof pack after fix. | Access control |
| F-002 | API team | 30 days | Remove sensitive fields and add serializer tests. | Rerun API validation. | Data protection |
| F-003 | Platform | 30 days | Upgrade affected component. | Rerun dependency and runtime checks. | Vulnerability management |

## Disclaimer

This report provides technical security evidence and framework mappings to support audit readiness, risk management, vendor reviews, and remediation planning. It does not certify compliance, replace legal advice, or substitute for a formal SOC 2 examination, ISO/IEC 27001 certification audit, HIPAA risk analysis, GDPR assessment, or regulator/auditor determination.
