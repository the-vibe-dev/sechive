# Bug Bounty Proof Patterns

## Executive Summary

Private bug bounty work is not copied raw into this repository. That is a feature, not a gap. Raw bounty evidence can contain report IDs, target names, account details, headers, cookies, tokens, screenshots, and unsubmitted vulnerability details. Publishing it would be irresponsible.

Instead, SecHive.ai converts private work into **root-cause proof patterns**: reusable descriptions of the weakness class, the evidence shape, the reporting standard, and the boundary between confirmed proof and candidate signal.

## What This Shows

The public-safe corpus demonstrates breadth without violating disclosure norms:

- Runtime authorization replay.
- Validation boundary bypass.
- Cross-domain or cross-asset logic abuse.
- Object authorization and role/property control failures.
- Source-first policy review patterns.
- Mobile/WebView trust-boundary analysis.
- Evidence packaging for HackerOne-style review.

## Why Pattern-Based Publication Is Better

A raw bug bounty report answers “what happened on one private target?” A root-cause pattern answers “can the system find this class of weakness again?”

For a buyer, the second question is often more important. SecHive.ai's value is not a single lucky exploit. It is the ability to repeatedly identify trust-boundary failures, validate them safely, and explain them in a report a triager or engineering team can act on.

## How The Pattern Is Formed

A public pattern should include:

1. The trust boundary being tested.
2. The application behavior that made the weakness plausible.
3. The evidence needed to confirm it.
4. The harm model.
5. The remediation model.
6. What is redacted or not published.

## Supporting Artifacts

- [Redacted bug bounty patterns](../studies/case-studies/redacted-bug-bounty-patterns.md)
- [Old-system source index](../studies/bug-bounty-patterns/old-system-source-index.md)
- [Runtime authorization replay](../studies/case-studies/runtime-authorization-replay.md)
- [Validation boundary bypass](../studies/case-studies/validation-boundary-bypass.md)
- [Cross-domain logic abuse](../studies/case-studies/cross-domain-logic-abuse.md)
