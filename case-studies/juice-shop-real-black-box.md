# Juice Shop Real Black-Box Run

## Executive Summary

OWASP Juice Shop is intentionally vulnerable, reproducible, and widely understood. That makes it useful for showing the shape of a completed SecHive.ai run without exposing a private customer. The included good run is not marketed as a secret target or a private production assessment. It is a transparent training-target assessment used to show workflow, evidence, and report output.

The included run preserved **39 validated runtime findings** in the final report artifacts.

## Why It Still Matters

A training target can still demonstrate real product behavior when the run is honest about its boundary:

- The target was interacted with as a running web application.
- Findings were promoted from observed runtime behavior.
- The report records vulnerability classes, impact, proof context, and remediation shape.
- The public repository redacts local network details and secrets.
- The result is not confused with a private customer pentest.

## What The Run Demonstrates

The run shows SecHive.ai handling a broad web application surface:

- Authentication bypass and weak authentication behavior.
- Authorization/object access weaknesses.
- SQL and NoSQL injection classes.
- Source/client disclosure signals.
- SSRF-style server-side fetch behavior.
- File upload and file read classes.
- Business logic and role/property control issues.
- Report generation and finding packaging.

## What It Does Not Claim

This case study does not claim that one Juice Shop run solved every challenge in every version. It also does not claim that public benchmark challenge answers are equivalent to a customer pentest. The claim is narrower:

> SecHive.ai can run a realistic web-app assessment loop, preserve validated findings, and produce customer-readable report material from a reproducible target.

## Supporting Artifacts

- [Completed run report, Markdown](../evidence/juice-shop/latest-good-run/run_report.md)
- [Completed run report, HTML artifact](../evidence/juice-shop/latest-good-run/run_report.html)
- [Completed run report, JSON](../evidence/juice-shop/latest-good-run/run_report.json)
- [Black-box example report](../studies/examples/juice-shop-blackbox-report.md)
- [White-box example report](../studies/examples/juice-shop-whitebox-report.md)
