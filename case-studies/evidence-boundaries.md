# Evidence Boundaries

## Executive Summary

A public evidence repository has to walk a tight line: enough detail to support the product claim, not so much detail that it leaks private targets, secrets, or reusable exploit material. SecHive.ai treats that boundary as part of the proof model.

## What Is Published

- Markdown case studies.
- Sanitized scorecards and aggregate benchmark results.
- Training-target reports with local/private values redacted.
- Public-safe screenshots and diagrams.
- Methodology documents.
- Redacted summaries of older CTF and bug bounty work.

## What Is Not Published

- Product source code.
- Raw logs, HAR files, packet captures, cookies, tokens, private keys, or authenticated browser storage.
- Private bug bounty reports or target-identifying evidence.
- CTF flags and challenge answer keys.
- Replay scripts intended to exploit live third-party systems.
- Local machine paths, private IPs, or operator environment details.

## Why Redaction Strengthens The Claim

Over-sharing can make a security case study look detailed while making it less trustworthy. A buyer should not have to wonder whether the repo leaked customer material. A reviewer should not have to separate useful proof from operational secrets.

By publishing sanitized evidence and clear boundaries, SecHive.ai makes the claim easier to evaluate:

- The result is visible.
- The method is visible.
- The proof standard is visible.
- The private material stays private.

## Supporting Policy

- [Repository redaction policy](../REDACTION_POLICY.md)
- [Manifest](../MANIFEST.json)
