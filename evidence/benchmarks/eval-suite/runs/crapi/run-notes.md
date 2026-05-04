# OWASP crAPI Run Notes

- Start: 2026-05-03T12:32:07-04:00
- Target URL: http://127.0.0.1:8888
- Container: crapi-web
- Purpose: API security benchmark and business logic review proof
- Scope: localhost/127.0.0.1 only
- SecHive command: `SECOPS_RUNTIME_ROOT=[REDACTED_PATH] SECOPS_DATABASE_URL=sqlite+pysqlite:///[REDACTED_PATH] python3 scripts/sechive scan http://127.0.0.1:8888 --mode pentest --json --strict-proof-only --proof-depth standard --local-test-env http://127.0.0.1:8888 --out [REDACTED_PATH]
- End: 2026-05-03T12:32:08-04:00
- Duration seconds: 1.0
- Status: completed (http-200)
- Output JSON: [REDACTED_PATH]

## Limitations
- This deterministic CLI run verified local target reachability and SecHive scope handling. Findings are not invented; empty finding output remains empty.
