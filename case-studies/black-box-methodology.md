# What Makes The Black-Box Tests Real

<p align="center">
  <img src="../assets/black-box-proof-loop.svg" alt="SecHive.ai black-box proof loop" width="100%">
</p>

## Executive Summary

SecHive.ai calls a run **black-box** only when the agent starts from the externally allowed target surface and builds its evidence from runtime observation. Source code, known solutions, private answer files, prior exploit notes, and benchmark ground truth are out of bounds for the black-box result.

That distinction matters. A system can look impressive if it is allowed to read source, inspect benchmark metadata, reuse prior answers, or count plausible guesses as findings. SecHive.ai's black-box case studies are built to answer the harder buyer question: **what can the system prove when it has to behave like an outside tester on an authorized target?**

## Black-Box Inputs

Allowed in black-box mode:

- The target URL, host, port, and explicit rules of engagement.
- Browser-visible pages, JavaScript, forms, routes, links, and error messages.
- Runtime HTTP responses, headers, redirects, cookies set by the target, and API behavior.
- Network/service observations collected during the current run.
- Agent-generated hypotheses based on what the target exposed during the current run.
- Benign proof attempts and controlled validation inside the authorized scope.

Not allowed in black-box mode:

- Local source trees supplied by the operator.
- Benchmark solution guides, flags, answer keys, or hidden scoring files.
- Prior run artifacts used as proof for the current run.
- Private bug bounty notes or leaked target-specific payloads.
- Human-provided exploit instructions that solve the case for the agent.

## What Counts As A Finding

A finding is not confirmed because the model names a vulnerability class. It must be backed by runtime evidence.

A confirmed black-box finding needs:

1. **Observed surface:** what the target exposed.
2. **Hypothesis:** why that surface may violate a trust boundary.
3. **Validation action:** the scoped request, browser action, or interaction used to test it.
4. **Observed result:** response, screenshot, status change, proof marker, or other target behavior.
5. **Impact explanation:** what harm the behavior would create in a real deployment.
6. **Boundary note:** what was not tested or not proven.

## Why This Is Different From A Scanner

Traditional scanners often stop at signatures: a version string, a reflected parameter, a missing header, or a known route. SecHive.ai treats those as **signals**, not findings. The system is expected to move from signal to hypothesis to proof, or explicitly record why proof was not reached.

That is why the case studies preserve negative evidence. A miss can be useful when it shows that the system refused to overclaim, stopped after a time budget, or separated a candidate path from a validated vulnerability.

## Why White-Box Is Reported Separately

White-box/source-aware testing is valuable. It finds deeper sinks faster, explains root cause better, and helps developers remediate. But it is not the same claim as black-box testing.

SecHive.ai reports white-box separately because:

- Source can reveal hidden routes and dangerous functions that an outside tester may not discover.
- Source can shorten hypothesis selection, which changes the difficulty of the task.
- Source candidates still need runtime validation before becoming confirmed findings.
- Mixing source-aware wins into black-box numbers would make the public claim weaker, not stronger.

## Why The XBOW Campaign Is A Good Signal

The XBOW-style campaign is useful because it contains many small, controlled web security tasks across different vulnerability families. It is not a replacement for customer pentesting, but it is a good repeatable signal for agent behavior:

- Does the agent recon correctly?
- Does it find the right route or trust boundary?
- Does it chain steps instead of stopping at the first clue?
- Does it recover proof from the live target?
- Does it preserve enough evidence for a reviewer?
- Does it avoid contaminating reusable skills with target-specific answers?

## Claim Boundary

SecHive.ai's public black-box claim is not “the model knows the answers.” The claim is narrower and stronger:

> Given an authorized target, a mode boundary, and a time budget, SecHive.ai can autonomously discover, test, validate, and report a large class of real web security weaknesses from runtime behavior alone, while preserving enough evidence for human review.
