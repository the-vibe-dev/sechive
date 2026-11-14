# XBOW-Style Black-Box Campaign

## Published snapshots

The retained May 2, 2026 [104-case manifest](../studies/benchmarks/xbow-full-manifest.json) supports **98 / 104 black-box wins**, **104 / 104 source-enabled wins** and **104 / 104 any-win cases**. These are best-of results after reruns, not first-attempt success rates.

A later published summary reports **99 / 104 black-box wins**. The matching updated case-level evidence is not in the public manifest, whose rows still sum to 98. The later figure is retained as a separate summary-only claim, rather than presented as the same case-backed campaign. See [snapshot reconciliation](../docs/benchmark-snapshots.md) and [machine-readable campaign records](../data/benchmark-record.json).

Exact per-case attempt counts, time budgets and model/tool versions are not fully published. The benchmark is a controlled engineering evaluation and does not establish a purchasable product, service guarantee or independent certification.

## What Was Tested

The campaign tested whether SecHive.ai could operate across many web security shapes without becoming a benchmark answer memorizer:

- Authentication and session flaws.
- Authorization and object access mistakes.
- Injection families.
- Server-side request and file-handling issues.
- Hidden routes and application behavior exposed through runtime observation.
- Multi-step chains where the first signal is not the final proof.

## Why This Is A Real Black-Box Signal

The black-box side used runtime interaction. It did not use source files, hidden benchmark metadata, answer keys, or supplied solution paths. The agent had to discover the application shape through the same kinds of signals an outside tester would use: routes, responses, browser behavior, forms, JavaScript exposed by the target, and controlled probes.

A black-box win was counted only when the retained evidence showed target-derived proof. Source-aware wins were counted separately.

## What Improved

Earlier public-safe docs carried a lower black-box number. The later summary reported 99 / 104 black-box wins. The archived per-case rows remain a separate 98 / 104 snapshot; publication of the corresponding updated case evidence is needed to reconcile them.

The improvement came from the same loop the product uses in normal testing:

1. Preserve failed hypotheses instead of throwing them away.
2. Identify whether the miss was recon, routing, payload shaping, validation, reporting, or evaluator accounting.
3. Upgrade reusable behavior, not case-specific answers.
4. Rerun within the same mode boundary.
5. Promote only when current-run proof exists.

## What The Remaining Gaps Mean

The retained manifest has six black-box gaps. The later 99-win summary implies five; its corresponding case update is not published. They are not hidden because hiding them would make the benchmark less useful.

Those gaps are useful product signals. They concentrate around the hardest parts of autonomous black-box testing:

- Blind inference where the target gives little feedback.
- Multi-step exploit chains where an early valid clue must be combined with a later unrelated primitive.
- Target health and timeout handling.
- Route-shape-specific API probing.
- Proof promotion and evaluator accounting when the proof format differs from the expected shape.

## Why White-Box Hit 104 / 104

White-box/source-aware mode gives the system a different advantage: it can inspect routes, sinks, config, handlers, and intended behavior before choosing a runtime validation path. SecHive.ai still requires runtime proof before promoting a source-derived candidate, but the source narrows the search space dramatically.

This is exactly why the product reports both numbers. Black-box shows outside-in autonomy. White-box shows developer-facing depth and remediation power.

## Public Claim Boundary

SecHive.ai does not claim an uncontested universal leaderboard win. Public results vary by mode, target set, time budget, scoring rules, and what counts as proof. The defensible claim is:

> The retained case snapshot supports 98 / 104 black-box and 104 / 104 source-enabled best-of wins. The later 99 / 104 black-box summary is a distinct, unreconciled publication.

## Supporting Artifacts

- [Full public-safe benchmark manifest](../studies/benchmarks/xbow-full-manifest.md)
- [Benchmark methodology](../methodology/benchmark-methodology.md)
- [Proof-pack methodology](../methodology/proof-pack.md)
