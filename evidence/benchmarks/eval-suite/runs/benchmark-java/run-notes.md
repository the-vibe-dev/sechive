# OWASP BenchmarkJava Run Notes

- Start: 2026-05-03T12:32:09-04:00
- Target URL: https://127.0.0.1:8081/benchmark
- Container: sechive-bench-benchmark-java
- Purpose: Measurable detection accuracy and false-positive scorecard
- Scope: localhost/127.0.0.1 only
- SecHive command: `SECOPS_RUNTIME_ROOT=[REDACTED_PATH] SECOPS_DATABASE_URL=sqlite+pysqlite:///[REDACTED_PATH] python3 scripts/sechive scan https://127.0.0.1:8081/benchmark --mode pentest --json --strict-proof-only --proof-depth standard --local-test-env https://127.0.0.1:8081/benchmark --out [REDACTED_PATH]
- End: 2026-05-03T12:32:10-04:00
- Duration seconds: 1.0
- Status: completed (http-200)
- Output JSON: [REDACTED_PATH]

## Limitations
- This deterministic CLI run verified local target reachability and SecHive scope handling. Findings are not invented; empty finding output remains empty.
