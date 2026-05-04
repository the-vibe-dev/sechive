# SecHive.ai Case Study Index

This is the GitHub-native portal. Every link below opens as Markdown in GitHub.

## Featured Studies

| Study | Claim Supported | Evidence Type |
| --- | --- | --- |
| [What Makes The Black-Box Tests Real](case-studies/black-box-methodology.md) | SecHive.ai black-box results are based on live target behavior, not source, answer keys, or benchmark leakage. | Methodology and control boundaries. |
| [XBOW-Style Black-Box Campaign](case-studies/xbow-black-box-campaign.md) | SecHive.ai reached 99 / 104 black-box wins and 104 / 104 white-box wins in the current public scorecard. | Aggregate benchmark evidence and mode separation. |
| [Juice Shop Real Black-Box Run](case-studies/juice-shop-real-black-box.md) | The system can perform a full web-app assessment against a reproducible intentionally vulnerable target. | Sanitized report artifacts and finding classes. |
| [Bug Bounty Proof Patterns](case-studies/bug-bounty-proof-patterns.md) | SecHive.ai finds real-world vulnerability classes while preserving disclosure boundaries. | Redacted root-cause pattern library. |
| [Customer-Ready Reporting](case-studies/customer-ready-reporting.md) | Runs become shareable reports with severity, harm, reproduction context, and remediation. | Sample customer report structure. |
| [Evidence Boundaries](case-studies/evidence-boundaries.md) | SecHive.ai does not overclaim when proof is missing. | Redaction and publication policy. |

## Supporting Materials

| Material | Link |
| --- | --- |
| Proof-pack methodology | [methodology/proof-pack.md](methodology/proof-pack.md) |
| Proof-pack format | [methodology/proof-pack-format.md](methodology/proof-pack-format.md) |
| Benchmark methodology | [methodology/benchmark-methodology.md](methodology/benchmark-methodology.md) |
| Sanitized benchmark evidence | [evidence/benchmarks/eval-suite/README.md](evidence/benchmarks/eval-suite/README.md) |
| Completed Juice Shop report | [evidence/juice-shop/latest-good-run/run_report.md](evidence/juice-shop/latest-good-run/run_report.md) |
| Redacted CTF/training summaries | [evidence/ctf-thm/README.md](evidence/ctf-thm/README.md) |

## What Buyers Should Notice

SecHive.ai does not ask the reader to trust a screenshot of a terminal. The repo is organized around defensible claims:

- Black-box and white-box results are reported separately.
- Source-assisted results are not used to inflate black-box claims.
- Negative evidence remains visible instead of being deleted from the story.
- Private bug bounty evidence is converted into reusable root-cause patterns, not pasted raw.
- Reports are written for customers, not just for operators.
