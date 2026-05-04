# OWASP WebGoat Run Notes

- Start: 2026-05-03T12:32:10-04:00
- Target URL: http://127.0.0.1:8080/WebGoat
- Container: vantix-bench-webgoat
- Purpose: Developer/AppSec remediation workflow demo
- Scope: localhost/127.0.0.1 only
- Vantix command: `SECOPS_RUNTIME_ROOT=[REDACTED_PATH] SECOPS_DATABASE_URL=sqlite+pysqlite:///[REDACTED_PATH] python3 scripts/vantix scan http://127.0.0.1:8080/WebGoat --mode pentest --json --strict-proof-only --proof-depth standard --local-test-env http://127.0.0.1:8080/WebGoat --out [REDACTED_PATH]
- End: 2026-05-03T12:32:12-04:00
- Duration seconds: 2.0
- Status: completed (http-200)
- Output JSON: [REDACTED_PATH]

## Limitations
- This deterministic CLI run verified local target reachability and Vantix scope handling. Findings are not invented; empty finding output remains empty.
