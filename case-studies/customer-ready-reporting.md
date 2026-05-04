# Customer-Ready Reporting

## Executive Summary

SecHive.ai is designed to produce reports that can be shared with customers, auditors, developers, and executives. A useful report is not just a list of vulnerability names. It needs to explain severity, evidence, harm, affected assets, reproduction context, remediation, residual risk, and what was not proven.

## What A Good SecHive.ai Report Contains

A customer-ready finding should include:

- Title and vulnerability class.
- Severity and CVSS vector where appropriate.
- Affected surface and scope boundary.
- Evidence summary, with proof-pack references when available.
- Reproduction context written safely, without publishing secrets.
- Potential harm in business language.
- Technical root cause.
- Recommended remediation.
- Validation status and residual uncertainty.

## Why This Matters Commercially

Security buyers are not only buying discovery. They are buying defensibility. A report has to survive questions like:

- Did this actually happen on our target?
- Can our engineers reproduce or reason about it?
- Is the severity justified?
- Is the evidence safe to share internally?
- Does the report separate confirmed issues from hypotheses?
- Can leadership understand why this matters?

SecHive.ai's reporting model is built to answer those questions.

## Supporting Artifacts

- [Sample compliance-ready report](../studies/examples/sample-compliance-ready-security-assessment-report.md)
- [Proof-pack methodology](../methodology/proof-pack.md)
- [Proof-pack format](../methodology/proof-pack-format.md)
- [Completed Juice Shop report](../evidence/juice-shop/latest-good-run/run_report.md)
