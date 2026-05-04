# Old System Bug Bounty Source Index

## Purpose

The older CTF and bug-bounty workspace was reviewed as a source of capability evidence, but raw program material was not copied into this public repository.

## Aggregate Inventory

- Program workspaces reviewed: 21
- Markdown evidence/report files observed within the bounded scan: 247
- JSON evidence files observed within the bounded scan: 237
- HTML evidence files observed within the bounded scan: 20

## Why Raw Files Are Excluded

Bug bounty artifacts often contain private target names, report IDs, session evidence, account-specific traces, headers, cookies, and unsubmitted vulnerability details. Publishing them would weaken privacy and responsible disclosure boundaries.

## What Was Preserved Instead

- Redacted public pattern studies under `studies/case-studies/`.
- General methodology for proof packs and evidence readiness under `methodology/`.
- Sanitized benchmark and training examples under `evidence/`.

## Publication Rule

Convert private bug bounty work into root-cause pattern studies only after target names, account details, report IDs, and replayable exploit material are removed.
