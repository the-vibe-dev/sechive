# SecHive Studies

Public-safe case studies, methodology notes, sanitized evidence, and customer-friendly examples for SecHive/Vantix security assessment capabilities.

This repository is intentionally content-only. It is meant to answer questions about what the system can do, how results are supported, and what a good completed run looks like without publishing product source code, private target data, raw operator state, credentials, personal details, or reusable exploit material.

## What Is Included

- Case studies for browser-led reconnaissance, authorization testing, benchmark assessments, reporting, and evidence handling.
- Methodology documents for proof packs, benchmark handling, evidence boundaries, and report readiness.
- Sanitized benchmark and Juice Shop run artifacts showing completed results and report formats.
- Redacted CTF and training summaries showing capability coverage without exposing flags or live lab secrets.
- A small static evidence portal under `portal/`.

## What Is Excluded

- Product source code and internal implementation files.
- Raw logs, cookies, session captures, authorization headers, tokens, private keys, and local runtime state.
- Raw bug bounty submissions or target-identifying evidence that could disclose private program details.
- Full replay scripts, destructive payloads, wordlists, or anything that turns a study into a weaponized runbook.
- Archives and generated cache folders.

## Start Here

- [Portal entrypoint](portal/index.html)
- [Public case studies](studies/public-case-studies.md)
- [Benchmark methodology](methodology/benchmark-methodology.md)
- [Proof pack methodology](methodology/proof-pack.md)
- [Redaction policy](REDACTION_POLICY.md)
- [Repository manifest](MANIFEST.json)

## Evidence Standard

Every included artifact should support at least one of these claims:

- The system can discover and prioritize realistic attack surfaces.
- The system can preserve evidence boundaries instead of overclaiming.
- The system can produce customer-friendly reports and proof material.
- The system can separate public-safe examples from private operational data.

When a source artifact was useful but not safe to publish, the portal includes a summary or aggregate inventory instead of the raw file.
