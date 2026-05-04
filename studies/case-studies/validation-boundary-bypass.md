# Validation Boundary / Denylist Bypass

**Pattern family:** validation and policy boundary bypass
**Source:** redacted bug bounty reports from the provided case-study corpus
**Disclosure status:** public-safe pattern only

<p>
  <kbd>CVSS Critical impact class</kbd>
  <kbd>policy bypass</kbd>
  <kbd>destination-side proof</kbd>
</p>

## Public Safety Boundary

This write-up keeps the vulnerability logic and review value, but removes
program names, repository names, package names, account identifiers, domains,
addresses, hashes, links, and reusable production exploit steps.

## Pattern Summary

The recurring issue is a split between where a policy is checked and where the
actual value-changing action occurs. A denylist, blocklist, finality threshold,
recipient validation, or governance constraint is enforced on one route or one
representation, while another route consumes a different representation without
reapplying the same policy.

The failure mode looks like this:

1. A value, account, recipient, validator, controller, or message is blocked by
   one policy boundary.
2. A second execution path receives a transformed, alternate, remote, or derived
   value.
3. The second path validates message shape or caller authority, but not the
   blocked object itself.
4. The protected action completes despite the object being policy-denied.

## Representative Redacted Findings

| Redacted ID | Pattern | Impact class |
| --- | --- | --- |
| BB-VAL-001 | Destination-side denylist omitted during inbound value release | Policy-bypassed mint or release |
| BB-VAL-002 | Outbound policy enforced, inbound recipient policy skipped | Blocked account receives value |
| BB-VAL-003 | Minimum finality threshold downgraded across a message boundary | Premature acceptance |
| BB-VAL-004 | Revoked delegate path still able to burn or withdraw | Role revocation bypass |
| BB-VAL-005 | Blocklisted authority can create a fresh mint path | Blocklist escape |

## Detection Logic

SecHive should route this family toward:

- `validation_bypass`
- `scope_guard`
- `bizlogic_hunter`
- `poc_validator`
- source-first reviewers when a policy function and an execution function can be
  compared directly

High-signal checks:

- Does the final execution value exactly match the value that was validated?
- Is the policy checked at the destination-side boundary, not only source-side?
- Are denylist/blocklist decisions carried forward as immutable execution facts?
- Are role revocations applied to outstanding approvals, sessions, and pending
  actions?
- Are canonicalization and decoding performed before the final policy check?

## Proof Standard

A strong proof for this family does not need production identifiers. It needs:

1. A blocked or policy-denied object.
2. A permitted alternate route that reaches the same protected action.
3. A before/after assertion showing the denied object changed state or received
   value.
4. A negative control showing the intended policy boundary rejects the direct
   path.

## Remediation Pattern

- Validate the exact object that will be consumed by execution.
- Re-run destination-side policy before mint, release, withdrawal, mutation, or
  role-sensitive action.
- Bind message parameters, hook calldata, derived recipients, and forwarded
  values back to the attested or originally approved object.
- Add regression tests for direct path rejection and alternate path rejection.

## Related Public Artifacts

- [Redacted bug bounty pattern inventory](redacted-bug-bounty-patterns.md)
- [OWASP Juice Shop white-box reference report](../examples/juice-shop-whitebox-report.md)
