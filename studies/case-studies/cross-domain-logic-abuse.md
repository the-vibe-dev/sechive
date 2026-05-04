# Cross-Domain / Cross-Asset Logic Abuse

**Pattern family:** authorization binding and asset-domain separation
**Source:** redacted bug bounty reports from the provided case-study corpus
**Disclosure status:** public-safe pattern only

<p>
  <kbd>CVSS Critical impact class</kbd>
  <kbd>asset-domain binding</kbd>
  <kbd>cross-boundary proof</kbd>
</p>

## Public Safety Boundary

This page preserves the vulnerability class and validation standard without
publishing target identities, private source paths, account details, production
calldata, proof artifacts, or reusable exploit payloads.

## Pattern Summary

Cross-domain logic abuse appears when a system validates an actor, message, or
outer operation, but fails to bind the final asset, tenant, token, repository,
domain, recipient, or controller back to that same authorization context.

The recurring shape is:

1. A valid authorization exists for object A in domain A.
2. Attacker-controlled forwarding, hook, callback, request, or manifest data
   introduces object B or domain B.
3. The system validates the outer authorization but trusts the inner object.
4. The operation consumes or mutates object B without authorization for that
   object/domain pair.

## Representative Redacted Findings

| Redacted ID | Pattern | Impact class |
| --- | --- | --- |
| BB-XD-001 | Withdrawal authorization can forward a different supported asset | Unauthorized reserve movement |
| BB-XD-002 | Forwarding calldata can rebind remote domain | Cross-domain value movement |
| BB-XD-003 | Address-book or manifest isolation fails across wallet boundary | Cross-account trust break |
| BB-XD-004 | Remote executable duplicate write crosses intended object binding | Repeated or misplaced state write |
| BB-XD-005 | Callback ticket or token can bridge login state across domains | Session confusion |

## Detection Logic

Vantix should route this family toward:

- `cross_domain_logic`
- `bizlogic_hunter`
- `api_security`
- `poc_validator`
- source/repo review when object ownership checks are visible in code

High-signal checks:

- Is the target object supplied by the client when it could be derived
  server-side?
- Is ownership checked on the object that actually changes state?
- Can hook data or forwarding calldata select a different asset, amount,
  recipient, repository, or domain than the validated outer request?
- Are pending authorizations invalidated when role, controller, or ownership
  state changes?

## Proof Standard

A review-ready proof should demonstrate:

1. Authorization for one object/domain pair.
2. A crafted but bounded request that switches the inner object/domain.
3. A successful side effect against the switched object/domain.
4. A remediation test proving the binding check blocks the mismatch.

## Remediation Pattern

- Derive object scope from the authenticated context where possible.
- Join authorization checks to object ownership at the final execution boundary.
- Bind forwarded parameters to the attested or approved operation.
- Reject any asset, amount, domain, recipient, repository, or tenant mismatch.
- Add negative tests for cross-domain object substitution.

## Related Public Artifacts

- [Redacted bug bounty pattern inventory](redacted-bug-bounty-patterns.md)
- [OWASP Juice Shop black-box reference report](../examples/juice-shop-blackbox-report.md)
