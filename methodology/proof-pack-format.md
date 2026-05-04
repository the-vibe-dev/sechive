# Proof Pack Format

Proof packs preserve enough evidence to review a finding without relying on screenshots or prose alone.

## Required Report Sections

Profile-based reports should include scope, methodology, evidence chain, reproduction, impact, remediation, limitations, redaction manifest, and signed proof manifest.

## Manifests

- `manifest.json`: profile, deliverables, compliance mapping, support archive metadata, trust summary.
- `redaction_manifest.json`: included, redacted, excluded, and blocked content.
- `signed_proof_manifest.json`: proof-pack paths, proof hashes, and signature status.

Unsigned manifests must say `unsigned-warning` rather than pretending cryptographic signing occurred.
