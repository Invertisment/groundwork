# Agent instructions for this repository

This repo is not an application — it's a reusable, language-agnostic architecture guideline for structuring AI-generated code so its boundaries stay mechanically verifiable rather than trusted on an AI's unverified word. When asked to help scaffold or build a *new* project (CI/CD, module layout, lint rules, contract tests), read `concepts/*.txt` first and generate that project's concrete verification predicates from them — do not bake project- or language-specific tooling back into this repo.

## What's here

- `concepts/` — six concept files, each defining one layer or cross-cutting rule by a property test (never by example list, never by LOC/file size), with a WHY, what belongs, edge cases, and testing guidance:
  - `FUNCTIONAL_CORE.txt` — pure, deterministic logic. No I/O, same input → same output.
  - `INFRA_LAYER.txt` — anything touching the OS/network/DB/hardware. Kept thin: anything testable without the real thing moves to core or saga. Tests are optional (outages and device quirks surface in production), but any fake must pass the same contract suite as the real thing. A "contract" is not the same thing as a language `interface` — the mechanism is language-dependent.
  - `SAGA_COMPOSITION.txt` — coordinating more than one independently-owned resource. Durable progress vs. a plain controller; may not exist for single-resource operations.
  - `GLUE.txt` — dumb wiring/framework entry points only, no decisions. Composition-root pattern covers DI without a framework.
  - `BOUNDARIES.txt` — where package/module cuts go: around business invariants, never around file size. First cut is human-gated; leaks are checked mechanically afterward.
  - `DECOMPOSITION.txt` — when to split a unit instead of implementing it directly. Empirical, not estimated up front. Two failure signatures trigger a split — stuck (fails after N attempts) and overfitting (passes by piling up branches special-cased to the given tests) — not pass/fail alone.
- `RAW_MESSAGES.txt` — the original raw conversation this guideline was distilled from. Historical source material, not authoritative: if it ever conflicts with `concepts/`, `concepts/` wins.
- `README.md` — one-paragraph orientation for humans.

## Rules to carry into any project generated from this guideline

- Never use LOC or file size as a split/complexity trigger, at any granularity (function, module, or package) — use the property tests in `DECOMPOSITION.txt` and `BOUNDARIES.txt` instead.
- Prefer generative/property-based tests over enumerated examples wherever a requirement can be expressed as an invariant — it's the strongest defense against an implementer special-casing branches to pass the specific tests it was shown.
- A "contract" is not necessarily a language `interface`. Pick the mechanism that fits the target language (nominal interface, structural interface, protocol/spec, or just a shared test suite where the language has no type layer) — see `INFRA_LAYER.txt`.
- Draw package/module boundaries around business invariants, not around file size or current code structure; verify them afterward with mechanical import-direction and purity checks.
- Keep the mechanical / agent-loop / human-gated split explicit per decision: import-direction and purity checks are mechanical; the too-big decompose loop is agent-native; the first boundary cut and the test-spec's faithfulness to the actual requirement stay human-gated.
- Never fully vibe the test harness itself — every other layer's correctness claim only holds if the harness verifying it is honest.
- Every project generated from this guideline has a `GROUNDWORK.md` in its root: a tiny file, short bullets only, recording which guideline commit was applied, the design choices made where the guideline is silent or ambiguous (with a one-line reason each), deliberate deviations from the current guideline, and known gaps. Update it whenever such a decision changes. The project's human README must mention that `GROUNDWORK.md` exists in one line. [Invertisment/qr-tools](https://github.com/Invertisment/qr-tools)'s is the worked example.
- Keep the project's own README condensed regardless of project size — a bigger project earns more docs elsewhere (`docs/`, code comments, ADRs), not a longer README. Front-load the single most orienting fact in the first line or two, list contents/instructions as short bullets rather than prose, and stay skimmable in one pass: a human should be oriented before they'd stop reading. This repo's own README and [Invertisment/qr-tools](https://github.com/Invertisment/qr-tools)'s are the two worked examples.
