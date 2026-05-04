# Redacted Bug Bounty Pattern Inventory

This document is the public-safe index built from the provided bug bounty report
corpus. It keeps the evidence shape, vulnerability class, and review value while
removing target names, domains, repository names, account identifiers, addresses,
hashes, private links, and reusable production exploit steps.

<p align="center">
  <kbd>90 public-safe results</kbd>
  <kbd>submitted through HackerOne</kbd>
  <kbd>accepted / closed as duplicate</kbd>
  <kbd>10 currently in review</kbd>
  <kbd>no payout claim</kbd>
</p>

## Proof-Site Summary

| Source family | Count | Public treatment |
| --- | ---: | --- |
| Bug bounty submissions and candidates | 90 | Redacted pattern inventory and top-find write-ups |
| OWASP Juice Shop black-box benchmark | 32 validated findings | Published in full and unredacted as reproducible benchmark evidence |
| OWASP Juice Shop white-box benchmark | 32 validated findings + 23 source candidates | Published in full and unredacted as reproducible benchmark evidence |

## Method Coverage

| Method family | Example proof shape |
| --- | --- |
| Business logic and runtime validation | Replay, duplicate execution, stale authorization, payment state mismatch |
| Source-first policy review | Denylist gaps, finality downgrade, controller revocation, unsafe validation boundary |
| Cross-domain protocol reasoning | Asset rebind, forwarding mismatch, bridge/cross-chain state inconsistency |
| Auth and identity | Step-up bypass, callback/token leakage, login-state confusion |
| Mobile and exported interfaces | Broadcast injection, exported providers, binder/interface exposure |
| Cloud and configuration | Secret exposure, public debug/RPC surfaces, static management dumps |

## Top Redacted Findings

| Pattern | Why it matters | Public write-up |
| --- | --- | --- |
| Runtime authorization replay | A one-time approval can execute repeatedly when nonce-like authorization material is not consumed. | [Runtime Authorization Replay](runtime-authorization-replay.md) |
| Validation boundary bypass | A denylist, blocklist, finality, or policy decision is enforced on one path but skipped at final execution. | [Validation Boundary / Denylist Bypass](validation-boundary-bypass.md) |
| Cross-domain logic abuse | A valid authorization for one object/domain can be rebound to another object/domain through hook, callback, forwarding, or raw input data. | [Cross-Domain / Cross-Asset Logic Abuse](cross-domain-logic-abuse.md) |
| Auth step-up bypass | Sensitive account changes proceed without the expected reauthentication boundary. | Included in inventory below |
| Mobile exported-interface abuse | Exported components or broadcast paths expose privileged data or action flows. | Included in inventory below |

## Reproducible Benchmark Evidence

The Juice Shop reports are intentionally published in full because they are based
on a training target and can be reproduced safely in a lab:

- [OWASP Juice Shop Black-Box Reference Report](../examples/juice-shop-blackbox-report.md)
- [OWASP Juice Shop White-Box Reference Report](../examples/juice-shop-whitebox-report.md)

## Full Redacted Inventory

| ID | Redacted result |
| --- | --- |
| BB-001 | Exported account log bridge exposes sensitive operational data |
| BB-002 | Open account interface brokers token material across a trust boundary |
| BB-003 | Cross-origin login-state endpoint leaks authentication context |
| BB-004 | Public cloud business-secret and auth bootstrap material exposure |
| BB-005 | External redirect candidate through developer `fromLocation` handling |
| BB-006 | Login iframe postMessage flow leaks credential-bearing state |
| BB-007 | Arbitrary callback ticket code leak in identity workflow |
| BB-008 | Ticket-to-cloud-session chain creates account takeover path |
| BB-009 | Wallet login broadcast permits token injection |
| BB-010 | Wallet real-name broadcast spoofing impacts identity state |
| BB-011 | Exported user-center provider exposes account data candidate |
| BB-012 | Key-share workflow permits share exfiltration |
| BB-013 | Internal leaf status mutation reaches privileged state |
| BB-014 | Static deposit privacy bypass exposes private transaction context |
| BB-015 | Exported NFC/TSM binder exposes wallet action candidate |
| BB-016 | View token exposes sensitive metadata |
| BB-017 | Read-only BGP configuration secret exposure |
| BB-018 | Read-only element-adoption credential exposure |
| BB-019 | Read-only wakeup privilege bypass |
| BB-020 | Read-only PPSK password exposure |
| BB-021 | Read-only settings secret exposure |
| BB-022 | Read-only VPN secret exposure |
| BB-023 | Static CMS management dump exposure |
| BB-024 | Unauthenticated JSON editor permits CMS access |
| BB-025 | Email rebinding proceeds without expected step-up authentication |
| BB-026 | Oversized cross-chain burn amount can blackhole destination mint |
| BB-027 | Validator registerer can hijack preassigned controller slots |
| BB-028 | Proposal parts fail to authenticate or preserve valid round |
| BB-029 | Malformed validator key triggers consensus panic |
| BB-030 | Validator set registry input triggers consensus panic |
| BB-031 | Wall-clock dependency creates consensus split risk |
| BB-032 | Zero-power active validator set triggers consensus panic |
| BB-033 | Wallet cold-storage address book authorization bypass |
| BB-034 | Wallet execution uninstall manifest isolation failure |
| BB-035 | Weighted multisig low-weight operation gas policy bypass |
| BB-036 | Weighted multisig runtime authorization replay |
| BB-037 | Cross-chain move burn amount blackholes destination mint |
| BB-038 | Invalid recipient bytes can blackhole cross-chain burns |
| BB-039 | Post-cancel request still permits outbound value transaction |
| BB-040 | Authorized sender escalation through operator forwarding |
| BB-041 | Remote executable duplicate write submission |
| BB-042 | Discovery bootstrap hijack |
| BB-043 | Engine payload denylist bypass |
| BB-044 | Minimum finality threshold bypass |
| BB-045 | Inbound receive path ignores destination denylist |
| BB-046 | Cancel attester can be rebound across payment network state |
| BB-047 | Global nonce collision blocks payment execution |
| BB-048 | Gateway batch overdraft creates unbacked credit |
| BB-049 | Gateway denylist exit path bypass |
| BB-050 | Commit certificate accepts extra forged signature as finalized |
| BB-051 | Proposal-only invalid value acceptance |
| BB-052 | Quorum certificate provider trust boundary failure |
| BB-053 | Future-round vote state denial of service |
| BB-054 | Input queue permits same-height unbounded preverification |
| BB-055 | Parts-only path synthesizes proposer input without authenticated proposal |
| BB-056 | Proposal-only path skips application validation |
| BB-057 | Round certificate invalid-signature replay denial of service |
| BB-058 | Same-round proposal equivocation causes evidence/WAL denial of service |
| BB-059 | Host marks incomplete proposal commitments valid |
| BB-060 | Parts-only path allows forged proposer-validity injection |
| BB-061 | Parts peer can impersonate proposer |
| BB-062 | Sync invalid bytes can stall node progress |
| BB-063 | Unsigned votes allow forged quorum certificates |
| BB-064 | Sync decision drops vote extensions |
| BB-065 | Authorization/IBC blacklist bypass |
| BB-066 | Replace-deposit flow bypasses post-blacklist policy |
| BB-067 | Controller removal does not revoke mint authority |
| BB-068 | Public chain RPC debug namespace exposure |
| BB-069 | Member discount authorized by raw personal data |
| BB-070 | Sensitive RPC namespace exposure |
| BB-071 | Inbound denylist bypass in alternate chain receive path |
| BB-072 | Minimum finality downgrade in alternate chain receive path |
| BB-073 | Gateway denylist does not freeze burn or withdrawal path |
| BB-074 | Revoked delegate can still burn and defeat pending withdrawal |
| BB-075 | Indirect object-store blocklist bypass |
| BB-076 | Blocklisted blocklister can self-unfreeze |
| BB-077 | Omitted blacklist migration unfreezes legacy blocked accounts |
| BB-078 | Blocklisted issuance authority can create fresh mint paths |
| BB-079 | Controller removal leaves mint authority active |
| BB-080 | Blocklisted multisig governance bypass |
| BB-081 | Stale controller approval after revocation still enables minting |
| BB-082 | Stale multisig approval after role revocation |
| BB-083 | Fake allow-asset path permits unbacked swap mint |
| BB-084 | Outbound messages use reserved zero nonce |
| BB-085 | Inbound denylist bypass in account-based receive path |
| BB-086 | Minimum finality downgrade in account-based receive path |
| BB-087 | Strong-auth snooze permits password-change bypass |
| BB-088 | Noncontiguous sync response creates liveness failure |
| BB-089 | Cross-domain forwarding replace hijack |
| BB-090 | Withdrawal forwarding token rebind moves a different supported asset |

## How To Read This Inventory

The IDs above are public tracking IDs, not original report IDs. The original
case-study corpus remains private because it contains target-specific details.
The public docs intentionally preserve the pattern, proof expectation, and
remediation shape without disclosing the live systems where the issues were
validated.
