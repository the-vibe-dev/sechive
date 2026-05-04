# OWASP BenchmarkJava SecHive Benchmark Report

## Benchmark

- Benchmark name: OWASP BenchmarkJava
- Local target URL: https://127.0.0.1:8081/benchmark
- Purpose: Detection accuracy, coverage, and false-positive scorecard
- Run status: completed
- Run duration seconds: 1.0
- Claim boundary: internally benchmarked against local intentionally vulnerable targets

## Why This Benchmark Matters

Detection accuracy, coverage, and false-positive scorecard. This benchmark is useful for repeatable security demos because it is intentionally vulnerable and can be started locally without touching public hosted targets.

## Exact SecHive Command

```bash
scripts/sechive scan https://127.0.0.1:8081/benchmark --mode pentest --json --strict-proof-only --proof-depth standard --local-test-env https://127.0.0.1:8081/benchmark --out runs/benchmark-java/sechive-output/sechive-scan-output.json
```

## Scope Boundaries

- Allowed hosts: `127.0.0.1`, `localhost`
- External egress: blocked where supported; not required for deterministic CLI reachability mode
- Target class: local intentionally vulnerable benchmark container
- Public targets scanned: none

## Summary Counts

- Total findings: 0
- Confirmed findings: 0
- Unconfirmed findings: 0
- Blocked actions: 0 recorded in this deterministic CLI run
- Out-of-scope refusals: 0 recorded for the local in-scope URL

## Finding Table

| Title | Severity | Status | Evidence |
| --- | --- | --- | --- |
| No findings promoted | n/a | completed | SecHive output did not contain promoted findings. |

## Severity Distribution

```json
{}
```

## CWE / OWASP Mapping

No unsupported vulnerability mappings are invented. When future SecHive findings include CWE or OWASP tags, this section should be generated directly from the evidence records.

## Evidence Artifacts

- `target-info.json`
- `scope-config.json`
- `sechive-output/sechive-scan-output.json`
- raw logs under `../../raw-logs/`
- screenshots under `screenshots/` when captured

## Safety Events

This run used local-only scope boundaries. No destructive actions were attempted. No public hosted target was scanned. No certification, compliance, or third-party attestation claim is made.

## Limitations And Next Steps

- Deterministic CLI mode confirms target reachability and preserves scope metadata; deeper browser/API findings require a full SecHive worker run with provider/runtime configuration enabled.
