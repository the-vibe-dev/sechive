# CLI Demo Notes

- Capture date: 2026-05-03
- Capture method: `script(1)`
- Transcript: `terminal-captures/sechive-cli-benchmark.typescript`
- Clean text transcript: `terminal-captures/sechive-cli-benchmark.txt`
- Scope: local benchmark containers bound to localhost only

## Demo Path

1. Health-check local benchmark targets.
2. Capture localhost listening ports and benchmark container metadata.
3. Run the SecHive benchmark wrapper against Juice Shop, crAPI, BenchmarkJava, WebGoat, and DVWA.
4. Generate reports, summaries, manifest, website copy, static index, and ZIP archive.

## Limitation

The CLI run used SecHive's deterministic URL scan path for repeatable local proof-pack packaging. It verified local target reachability and scope handling, but did not invent or validate vulnerabilities where the current CLI output contained none.
