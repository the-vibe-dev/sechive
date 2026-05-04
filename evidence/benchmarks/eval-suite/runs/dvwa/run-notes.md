# DVWA Run Notes

- Start: 2026-05-03T12:32:12-04:00
- Target URL: http://127.0.0.1:8088
- Container: vantix-bench-dvwa
- Purpose: Classic vulnerability smoke baseline
- Scope: localhost/127.0.0.1 only
- Vantix command: `SECOPS_RUNTIME_ROOT=[REDACTED_PATH] SECOPS_DATABASE_URL=sqlite+pysqlite:///[REDACTED_PATH] python3 scripts/vantix scan http://127.0.0.1:8088 --mode pentest --json --strict-proof-only --proof-depth standard --local-test-env http://127.0.0.1:8088 --out [REDACTED_PATH]
- End: 2026-05-03T12:32:13-04:00
- Duration seconds: 1.0
- Status: completed (http-200)
- Output JSON: [REDACTED_PATH]

## Limitations
- This deterministic CLI run verified local target reachability and Vantix scope handling. Findings are not invented; empty finding output remains empty.
