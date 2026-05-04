# Redaction Policy

This repository is built from sanitized examples only.

## Allowed

- Public benchmark target names and intentionally vulnerable training applications.
- Sanitized screenshots, scorecards, report summaries, and executive writeups.
- General vulnerability classes, methodology, validation criteria, and evidence expectations.
- Redacted request and response examples when they are needed to explain a finding.

## Redacted Or Omitted

- Real private target domains, internal IP addresses, local hostnames, and operator workstation paths.
- Credentials, bearer tokens, session cookies, API keys, JWTs, TOTP secrets, password hashes, and private keys.
- CTF flags and one-time challenge answers.
- Raw browser storage, HAR files, packet captures, terminal logs, and authenticated replay scripts.
- Bug bounty report IDs, private program names where not explicitly public, and unsubmitted vulnerability evidence.
- Payload-only material that is not necessary to support a customer-facing study.

## Replacement Markers

- `[REDACTED_HOST]` for private hosts, lab IPs, or local addresses.
- `[REDACTED_TOKEN]` for session tokens and JWT-like values.
- `[REDACTED_SECRET]` for secrets, hashes, flags, keys, and sensitive values.
- `[REDACTED_PROGRAM]` for private bug bounty targets or programs.
- `[REDACTED_PATH]` for local runtime paths that expose operator environment details.

## Publication Rule

If an artifact cannot be sanitized without weakening the evidence boundary or exposing sensitive operational detail, publish a short summary and keep the raw material out of this repository.
