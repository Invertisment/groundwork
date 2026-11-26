# Agent instructions for this repository

This repo is not an application — it's a reusable, language-agnostic architecture guideline for structuring AI-generated code so its boundaries stay mechanically verifiable rather than trusted on an AI's unverified word. When asked to help scaffold or build a *new* project (CI/CD, module layout, lint rules, contract tests), read `concepts/*.txt` first and generate that project's concrete verification predicates from them — do not bake project- or language-specific tooling back into this repo.

## What's here

- `concepts/` — the concept directory: one file per layer or cross-cutting rule, each defining it by a property test (never by example list, never by LOC/file size), with a WHY, what belongs, edge cases, and testing guidance. Read all of them before scaffolding. They're dense on purpose: written for agents, every line a rule. Humans review them, but a skimming human isn't the reader, so don't pad, summarize or restructure them for one.
- `RAW_MESSAGES.txt` — the original raw conversation this guideline was distilled from. Historical source material, not authoritative: if it ever conflicts with `concepts/`, `concepts/` wins.
- `README.md` — one-paragraph orientation for humans.

## Rules to carry into any project generated from this guideline

Everything in the concept directory carries over. These are the rules no concept file states:

- Keep the mechanical / agent-loop / human-gated split explicit per decision: import-direction and purity checks are mechanical; the too-big decompose loop is agent-native; the first boundary cut and the test-spec's faithfulness to the actual requirement stay human-gated.
- Never fully vibe the test harness itself — every other layer's correctness claim only holds if the harness verifying it is honest.
- Every project generated from this guideline has a `GROUNDWORK.md` in its root: a tiny file, short bullets only, recording which guideline commit was applied, the runtime envelope (each target and the limits that bind there, see `concepts/RUNTIME_ENVELOPE.txt`), the design choices made where the guideline is silent or ambiguous (with a one-line reason each), deliberate deviations from the current guideline, and known gaps. Update it whenever such a decision changes. The README mentions it in one line (see `concepts/HUMAN_READING.txt`). [Invertisment/qr-tools](https://github.com/Invertisment/qr-tools)'s is the worked example.
- A project already on this guideline moves to a newer commit by diffing `concepts/` and this file from the commit its `GROUNDWORK.md` pins, then applying each new or changed rule or recording it as a deviation or known gap. The pin moves only once every one of them is handled, so it never names a commit whose rules the project hasn't dealt with.
- Every generated project also has a thin `AGENTS.md` in its root, the entry point agent tools load on their own (README and `GROUNDWORK.md` aren't). It holds only: read `GROUNDWORK.md` first; the one command that runs every check (and that it formats files in place first, see `concepts/HUMAN_READING.txt`); the rules no mechanical check enforces (glue handlers only call one core transition, new logic goes to core with property tests, agent-written tests are checked by mutation, search the project for an existing definition before writing a new one; which libraries a layer may use is REUSE.txt's, enforced by the allow list); and that a decision that changes or deviates from the guideline gets a `GROUNDWORK.md` bullet. A pointer, not a copy: repeating `GROUNDWORK.md` or the concepts there lets them drift apart. Tools that read another file name get a one-line pointer to it (e.g. a `CLAUDE.md` containing `@AGENTS.md`).
