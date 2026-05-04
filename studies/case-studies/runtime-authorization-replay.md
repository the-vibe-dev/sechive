# Runtime Authorization Replay

**Pattern ID:** `replay_auth`
**Category:** Business Logic
**Public status:** Redacted from validated external findings

<p>
  <kbd>CVSS High/Critical pattern</kbd>
  <kbd>HackerOne-style proof</kbd>
  <kbd>replay invariant</kbd>
</p>

## Public Safety Boundary

This page generalizes a real externally validated vulnerability pattern. It does
not include target names, production accounts, customer data, private repository
paths, credentials, keys, domains, or exploitable production payloads.

## What Vantix Found

A runtime authorization workflow accepted a signed one-time action. The signed
material included a nonce-like value, but neither the validation layer nor the
execution wrapper consumed that nonce in durable state. Possession of one valid
authorization was enough to execute the same privileged action more than once.

The important invariant was:

> one signed runtime authorization must authorize one execution.

The observed behavior violated that invariant:

1. A valid action was signed by the required authorizers.
2. The relayer submitted the signed authorization and the action succeeded.
3. The relayer submitted the exact same authorization again.
4. The second execution also succeeded because the nonce only changed the digest;
   it was never marked as consumed.

## Detection Path

- Skill: `bizlogic_hunter`
- Skill: `api_security`
- Skill: `poc_validator`
- Signal: authorization material contains nonce, request ID, or intent ID
- Signal: validation function is read-only or detached from execution state
- Signal: repeated execution succeeds without signature mutation
- Validation: regression proof reuses the same authorization bytes twice and
  confirms repeated side effects

## CVSS

`CVSS:4.0/AV:N/AC:L/AT:N/PR:L/UI:N/VC:H/VI:H/VA:L/SC:H/SI:H/SA:L`

This vector is retained as public-safe scoring context. The point of the case
study is not the exact score alone; it is the proof shape: a supposedly
single-use authorization executes more than once.

## Why It Matters

Replayable runtime authorization breaks the trust boundary between approval and
execution. In value movement, governance, repository, identity, or privileged
automation flows, the result can be repeated transfer, repeated state mutation,
or execution of actions beyond what the approving parties intended.

## Remediation Pattern

- Consume the nonce, intent ID, or payload hash in the same transaction boundary
  as the protected action.
- Bind replay protection to actor, account, validation function, and action
  context.
- Reject repeated authorizations before any side effects.
- Add regression tests that submit the exact same signed payload twice.

## Public Proof Surface

This pattern appears in the docs proof set as:

- Top-level proof page: [Bug Bounty Proof Pack](bug-bounty-proof.md)
- Full appendix: [Redacted Bug Bounty Pattern Inventory](redacted-bug-bounty-patterns.md)
- Reproducible benchmark counterpart: [OWASP Juice Shop Proof Pack](juice-shop-proof.md)
