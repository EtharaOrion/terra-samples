<p align="center">
  <img src="assets/hero.jpeg" alt="Terra: real-world, multi-step, multi-modal tasks for general AI assistants, scored by a verifiable exact-match reward" width="880">
</p>

<p align="center">
  <strong>Measuring general-assistant competence on real-world, multi-step, multi-modal tasks, scored by a verifiable exact-match reward.</strong>
</p>

<p align="center">
  <a href="#summary"><img alt="Built by Ethara.AI" src="https://img.shields.io/badge/built%20by-Ethara.AI-2e7d4f.svg"></a>
  <a href="#the-30-task-cohort"><img alt="Cohort: 30 tasks" src="https://img.shields.io/badge/cohort-30_tasks-3fa06f.svg"></a>
  <a href="#curated-task-01"><img alt="Curated: 1 of 30" src="https://img.shields.io/badge/curated-1_of_30-8b5a2b.svg"></a>
  <a href="#scoring-methodology"><img alt="Reward: no judge in the loop" src="https://img.shields.io/badge/reward-no_judge_in_the_loop-845EF7.svg"></a>
  <a href="#verification-and-quality-assurance"><img alt="Reward: verifiable, never estimated" src="https://img.shields.io/badge/reward-verifiable%2C_never_estimated-a83232.svg"></a>
</p>

<p align="center"><sub>
  <a href="#summary">Summary</a> · <a href="#governance-the-trinity">Trinity</a> · <a href="#the-30-task-cohort">Cohort</a> · <a href="#curated-task-01">Task 01</a> · <a href="#repository-layout">Layout</a> · <a href="#task-bundle-structure">Bundle</a> · <a href="#trajectory-structure">Trajectories</a> · <a href="#scoring-methodology">Scoring</a> · <a href="#what-this-sample-withholds">Withheld</a> · <a href="#reproduction">Reproduction</a> · <a href="#verification-and-quality-assurance">Verification</a> · <a href="#limitations">Limitations</a>
</sub></p>

# Terra: 30-Task Evaluation Sample

**Terra measures whether an agent can complete real-world assistant tasks, not just answer trivia.**
Each task is a question that is simple for a capable human but hard for a current frontier model: it
takes several steps and often more than one modality to reach the answer, coordinating web
retrieval, document and file reading (PDF, spreadsheet, CSV, JSON), image understanding, and
multi-step quantitative reasoning. Every task resolves to a single unambiguous answer that a
verifier checks by **exact match**, so the reward is a clean binary signal with **no judge model in
the reward path**.

This repository is the public sample of the Terra corpus authored under the
[Trinity](https://github.com/Ethara-Ai/trinity) contracts. The current authoring wave is a
**30-task cohort** (Level 1 = 6, Level 2 = 10, Level 3 = 14). Task formats, environment pinning,
verifier contract, and trajectory formats here are identical to the production benchmark.

> ### Status: `1 of 30 curated`
>
> One task is fully authored, byte-frozen, verified, and has recorded trajectories:
> [`8c29ea6d-4554-5656-9cea-d5034ae6156b`](#curated-task-01) (`weighted-mean-top2-date-window`,
> Level 1). Task 02 is in authoring as a provisional bundle. Tasks 03 to 30 are planned and
> specified but not yet authored.
>
> Terra publishes numbers only from recorded, scored runs. With one curated task there is **no
> cohort pass rate to report**, and none is projected here. The FORGE authoring disposition is
> `HOLD:PILOT_REQUIRED`, the CRUCIBLE audit disposition is `HOLD`, and the ENGRAM ledger is
> `STALE` (honest scaffold, not a failure). Those ceilings lift only when an external signed pilot
> over frozen bytes lands. See [Verification](#verification-and-quality-assurance).

## Summary

| Property                | Value                                                                                  |
| :---------------------- | :------------------------------------------------------------------------------------- |
| Cohort size             | **30** tasks (Level 1: 6 / Level 2: 10 / Level 3: 14)                                   |
| Curated and frozen      | **1** (`weighted-mean-top2-date-window`); 1 in authoring; 28 specified, not yet authored |
| Difficulty levels       | 3, fixed at authoring time from required reasoning and tool steps                        |
| Archetype               | `general-assistant`                                                                     |
| Score                   | binary exact match in {0, 1}, per run in `summary.json` (`score`)                        |
| Reward path             | deterministic normalizer plus string compare; no LLM judge                              |
| Modalities              | pure computation, structured data (CSV / XLSX / JSON), PDF, image, web, multi-modal      |
| Anchored hardness rows  | 40 catalogued; **27** targeted by this cohort                                            |
| Delivery format         | Harbor, with `@sha256:` pinned images and a declared network policy                      |
| Contract source         | `trinity/ENGRAM.md`, `trinity/FORGE.md`, `trinity/CRUCIBLE.md`                           |

A separate, earlier 20-task validation pilot over the GAIA-derived corpus (Kimi K2.5 and Qwen3-VL)
is reported in the [knowledge repo](https://github.com/EtharaOrion/terra#-results). That is a
different corpus lineage and its rates do not carry over to this cohort.

## Governance: the Trinity

Terra tasks are not hand-blessed. Three adversarial instruments own the whole lifecycle with no
overlap and no gap, and every claim in this document traces to one of them.

| Instrument   | Owns exactly one job                                                    | Harness        | Root report              |
| :----------- | :---------------------------------------------------------------------- | :------------- | :----------------------- |
| **ENGRAM**   | Remembers across tasks, expires levers the frontier has caught, directs the other two. The master. | `memory/`      | `DIRECTIVE.md`, `HARDNESS.md` |
| **FORGE**    | Authors and proves tasks, one per invocation.                            | `seed/`        | `EDICT.md`               |
| **CRUCIBLE** | Audits the complete project against instrumented evidence.               | `audit/`       | `VERDICT.md`             |

Three properties of that arrangement are load-bearing for anyone reading these numbers:

- **The reward-hacking firewall.** There is no direct channel between the author and the auditor. A
  direct bridge would let an author tune a task toward whatever the auditor passes. FORGE never
  reads CRUCIBLE output, CRUCIBLE never feeds findings back, and ENGRAM is the only bridge, exposing
  its ledger through two computed projections (`FORGE_VIEW`, `CRUCIBLE_VIEW`) that strip every
  forbidden field and fail closed if one survives.
- **Difficulty is measured, never claimed.** No authored probability counts. A task designed to
  defeat a hardness lever is *unproven*, not *hard*, until an external signed pilot against a frozen
  solver registry says so, and that evidence expires as the frontier improves.
- **Everything fails closed.** A missing answer, a missing attachment, a truncated trace, or a
  missing control scores incorrect or caps the disposition at `HOLD` / `BLOCK`. A coverage gap is a
  finding, never a silence.

## The 30-task cohort

Levels are a property of the task, fixed at authoring time from the number of reasoning and tool
steps a correct solution requires, and decided before any model runs. They describe the task, not
what a given model happened to experience.

| Level | Tier           |  n  | Definition                                                                                      |
| :---- | :------------- | --: | :---------------------------------------------------------------------------------------------- |
| 1     | `intermediate` |   6 | No tool or one tool, no more than roughly 5 steps.                                                |
| 2     | `advanced`     |  10 | Roughly 5 to 10 steps; combining different tools is needed.                                        |
| 3     | `expert`       |  14 | For a near-perfect assistant: arbitrarily long action sequences, any number of tools, the open world. |

### Modality distribution

| Modality                         | L1 | L2 | L3 | Total |
| :------------------------------- | -: | -: | -: | ----: |
| Pure computation                 |  3 |  2 |  2 |     7 |
| Structured data (CSV, XLSX, JSON) |  1 |  2 |  2 |     5 |
| PDF extraction                   |  1 |  1 |  2 |     4 |
| Image and visual reasoning       |  1 |  2 |  2 |     5 |
| Web retrieval                    |  0 |  2 |  3 |     5 |
| Multi-modal composition          |  0 |  1 |  3 |     4 |
| **Total**                        |  **6** | **10** | **14** | **30** |

No modality clusters on a single level: Level 3 spreads across all six, Level 2 across five, Level 1
across four.

### Authoring constraints

Every task in the cohort is authored to satisfy all four of the following, and to defeat at least
two of the 40 published hardness levers catalogued in
[`HARDNESS.md`](https://github.com/EtharaOrion/terra/blob/main/HARDNESS.md), each backed by an arXiv
paper stored in the shared `research/` corpus. The cohort targets 27 distinct rows.

1. **One correct answer.** The question fixes the answer surface (rounding rule, number of
   fractional digits, casing, punctuation, separators) so exactly one string matches under exact
   match. Ambiguity is a blocking defect, not a rounding error.
2. **No one-hop shortcut.** The ground truth is not findable on the first page of a search for the
   question. Where a task pins a public source, it composes at least one filter or transformation
   that a search result cannot short-cut.
3. **No recall without execution.** Synthetic constants, freshly authored per task, are the
   anti-recall barrier; a model that has seen the source still cannot produce the answer without
   running the procedure. Every task is probed against a named reference adversary before authoring
   records it as hard.
4. **Time-invariant.** Web-sourced tasks pin a Wikipedia `oldid`, an arXiv version tag, or an
   institutional publication date. Attachment-backed tasks are byte-frozen. The answer does not
   drift.

## Curated task 01

The one task authored, frozen, verified, and delivered so far.

| Field                | Value                                                            |
| :------------------- | :---------------------------------------------------------------- |
| `task_id`            | `8c29ea6d-4554-5656-9cea-d5034ae6156b`                            |
| Name                 | `weighted-mean-top2-date-window`                                  |
| Level / tier         | 1 / `intermediate`                                                |
| Archetype            | `general-assistant`                                               |
| Failure mode         | `chained_precision_survival`                                      |
| Difficulty axis      | `Agentic`                                                         |
| Modality             | pure computation, self-contained (0 attachments, 1 turn)          |
| Author's solve       | 4 steps, 1 tool                                                   |
| Grading mode         | `exact-match`                                                     |
| Answer type          | `scalar_decimal_exactly_four_fractional_digits`                   |
| Agent environment    | `python:3.11.9-slim-bookworm@sha256:8fb099...c60c317`, `no_network` |
| Verifier environment | same pinned image, `no_network`, `separate_from_agent_environment` |
| Bundle release hash  | `sha256:ed747e5f700c62268593080ca31e2244f293d876f9d82304a4feca2567f30a66` |

**What it asks.** The prompt embeds two tables inline: 15 dated station readings and a per-station
weight table. The agent must (1) total every reading per station with no filter applied and take the
two largest, (2) restrict to an inclusive date window *and* to that top-2 pair, (3) compute the
weighted arithmetic mean over the surviving rows, and (4) round half-away-from-zero to exactly four
decimal places, emitting the result on a single line behind a `FINAL ANSWER:` marker.

**Why it is hard for its level.** The task is three lines of arithmetic for a human with a
spreadsheet, and its trap is ordering. The top-2 selection is defined over the *unfiltered* data
while the mean is computed over the *filtered* data. Applying the date window before the ranking
selects a different station pair and yields a confidently wrong number that is still four decimal
places wide and still looks like an answer. Exact match gives that trajectory nothing. The failure
mode is catalogued as `chained_precision_survival`: precision must survive every link of the chain,
and a single slip anywhere zeroes the run.

**Why it is self-contained.** The task declares `no_network` with an explicit rationale: the puzzle
carries its own data, so live retrieval is neither required nor permitted. The reading values,
weights, and window are freshly authored bytes; a single-byte edit re-derives the task UUID.

**Recorded runs.** Both recorded runs reached the frozen answer. This is one Level 1 task at one run
per model, so it is evidence that the oracle and the harness agree end to end, and it is **not** a
pass rate and **not** a difficulty measurement.

| Run directory       | Role                          | Score | LLM calls | Prompt tok. | Completion tok. |    Cost | Avg latency |
| :------------------ | :---------------------------- | :---- | --------: | ----------: | --------------: | ------: | ----------: |
| `opus-4.8`          | graded cohort run             | `1`   |         4 |      77,576 |           1,011 | $0.2174 |      7.94 s |
| `opus-5-reference`  | held-out reference (not a cohort score) | `1`   |         5 |     107,078 |           2,437 | $0.5963 |      8.08 s |

The held-out reference run enforces the other side of the verifier gate: a verifier that only ever
sees passing runs is unproven. It is marked `.oracle_reference` and is excluded from any rate.

In Harbor delivery format the same run also carries a layered concern report: gate `accept`,
`graded_score` 0.9231, pytest pass rate 1.0, rubric scored, with every Layer-0 gating concern
(`RUN_COMPLETE`, `OUTCOME_SIGNAL_TRUSTWORTHY`, `RESULT_INTEGRITY`) passing.

## Repository layout

```
terra-samples/
├── README.md                  # this document
├── LICENSE
├── assets/
│   └── hero.jpeg
├── dataset/                   # agent-visible task bundles, one directory per UUID
│   └── <uuid>/ ...
└── trajectories/              # recorded runs, one directory per UUID and model
    └── <uuid>/<model>/ ...
```

Task UUIDs match **1:1** between `dataset/` and `trajectories/`. UUIDs are derived, not assigned:
`uuid5(FORGE_TASK_NAMESPACE, canonical_content_hash)` under the genesis-pinned namespace
`c53e8f3b-526f-52c0-a04e-89e2269b237d`, so the identifier is a function of the frozen bytes and any
edit produces a different task.

Today this repository ships the document and the hero asset only. Bundle and trajectory directories
land here as tasks clear the authoring gate, under the publication rule in
[What this sample withholds](#what-this-sample-withholds).

## Task bundle structure

Every task ships in **Harbor delivery format**, and that format is a property of the frozen bytes
rather than a packaging step performed afterwards:

```
dataset/<uuid>/
├── task.toml               # manifest: uuid, name, level, metadata, environment, answer surface
├── instruction.md          # the only agent-visible prompt bytes
├── environment/            # agent-side environment definition
├── tests/
│   ├── test.sh             # verifier entry point
│   ├── verifier.py         # deterministic grader
│   └── reward.json         # reward contract the verifier writes
└── solution/               # PRIVATE oracle tree, never published (see below)
```

`task.toml` pins the contact surface: schema version, the `@sha256:` digest of every container
image, the declared network policy, the verifier environment mode, and the reward contract path. A
format change moves the sign-off digest, so conformance cannot be bolted on later. The bundle is
proven to rebuild and grade on a clean host from its pinned image digest alone; an
authoring-host-only rebuild is a blocking defect.

## Trajectory structure

Each run lives under `trajectories/<uuid>/<model>/`:

```
trajectories/<uuid>/<model>/
├── summary.json                    # graded result: score, model_answer, ground_truth, cost, tokens, latency
├── metadata.json                   # run configuration and environment
├── output.jsonl                    # full step-by-step agent trace (actions + observations)
├── output.critic_attempt_*.jsonl   # critic re-check passes over the final answer
├── logs/                           # per-instance runner logs
└── conversations/                  # per-turn message logs
```

Alongside the model directories, `trajectories/<uuid>/verification/` carries the verifier tree
(`verifiers/pytest/`, `verifiers/rubric/`, `verifiers/coverage.json`) and the per-model results
(`results/<model>/report.json`, `pytest_results.json`, `pytest_results.xml`, `rubric_results.json`).

Key `summary.json` fields: `score` (boolean exact-match reward), `model_answer` and `ground_truth`
(what the agent produced versus the frozen answer), `cost` (USD), `prompt_tokens`,
`completion_tokens`, `llm_calls`, and `avg_latency_seconds`. The model is identified by its
directory name.

## Scoring methodology

The verifier assigns each run a binary score:

```
score = 1  if normalize(model_answer) == normalize(ground_truth)  else  0
```

The answer is extracted from the trace by a single marker line. The matcher is deliberately
permissive about *framing* and strict about *content*: it accepts the marker under agent-scaffold
prefixes (indentation, speaker names, ordinals, timestamps) and then takes the first whitespace
token, and it rejects any output carrying more than one marker. Normalization is deterministic
(trimming, casing, numeric and format canonicalization) and configured per task by a
`normalizer_config.yaml` held in the private FORGE harness rather than in the shipped bundle. There
is **no judge model anywhere in the reward path**, so the score is
free of the position, verbosity, and self-preference biases documented for LLM-as-judge scoring.

A run solves a task only when its `score` is exactly 1. Missing or unparseable answers **fail
closed** as incorrect, never skipped.

Ground truth is **generated, never authored**. A private `recompute.py` derives the human-readable
truth statement, the checker fixtures, and the executable oracle from one source file in a single
pass, so the truth a reviewer reads, the fixtures the checkers assert, and the oracle the harness
runs cannot disagree. Task 01 additionally carries an independent external anchor
(`kind_B_different_transformation_path`): a second solver that reaches the same answer by a
different route, so a bug in one derivation cannot silently define the ground truth.

## What this sample withholds

A public sample that ships the answer key is a contaminated benchmark. Two rules apply here:

- **`solution/` and `TRUTH.md` are never published.** They carry the gold answer and the issued
  canary tokens, and they are forbidden fields in both ENGRAM projections. They exist in the private
  delivery bundle so the frozen bytes re-derive byte-identically under verification, and they stop
  there.
- **Gold answers are not printed in this document.** Task 01's answer surface is documented (scalar
  decimal, exactly four fractional digits, half-away-from-zero) and its value is not. Anyone
  evaluating an agent needs the shape, not the string.

The agent never sees the ground truth during a run. Any run that edited the graded answer or reached
for a reference solution is rejected rather than scored.

## Reproduction

No trajectory re-execution is needed to check a published rate: every score ships with the run.

```python
import json, glob, collections

def solved(run_dir):                      # a run "solves" iff score is true
    return bool(json.load(open(f"{run_dir}/summary.json"))["score"])

by_model = collections.defaultdict(list)
for sm in glob.glob("trajectories/*/*/summary.json"):
    model = sm.split("/")[-2]
    if model.endswith("-reference"):      # held-out reference runs are not cohort scores
        continue
    by_model[model].append(solved(sm.rsplit("/", 1)[0]))

for model, runs in sorted(by_model.items()):
    print(f"{model} pass@1 = {sum(runs) / len(runs):.3f}  (n={len(runs)})")
```

Per-level rates come from joining each run's `score` to its task `level` in `dataset/<uuid>/task.toml`.
Agent execution and scoring are orchestrated by the
[Terra harness](https://github.com/EtharaOrion/terra-harness); a trajectory is valid only against
the pinned `harness` plus `dataset` pair it was produced under, and re-scoring the same trace
against the same frozen bytes must reproduce the same reward.

## Verification and quality assurance

Every task clears a layered gate before it enters this sample.

- **Structure.** Dataset records and trajectory directories match 1:1 by UUID; required files
  present and non-empty in every run; the model grid complete.
- **Deterministic audit.** Every required field present and well-formed (`uuid`, `level` in 1 to 3
  agreeing with its tier, a non-empty answer with a declared surface, matching attachment paths);
  each attachment resolves, opens, and is the declared type; every `score` reconciles against
  `model_answer` versus `ground_truth` under exact match.
- **Verifier robustness.** The grader is itself an attack surface, so it is proven rather than
  assumed. Task 01's verifier was replayed against a 6-perturbation by gold/wrong matrix
  (indentation, name prefix, ordinal suffix, timestamp prefix, and all combined) and scores gold 1.0
  and wrong 0.0 in every cell, while rejecting all three multi-marker cases with
  `more_than_one_final_answer_marker_found`. An earlier bundle that failed this matrix was retired,
  not patched in place.
- **Adversarial pass.** FORGE Phase 3 runs a Hacker, Fixer, and Solver loop plus a ShortcutScore
  gate before any task is called hard.
- **Human validation.** Human sign-off gates are enforced by SHA-256: a human writes the digest of
  the candidate contract into an `approved` file, and the scaffolder recomputes it before writing
  anything or exits. A capability-state flip re-triggers sign-off.
- **Independent audit.** CRUCIBLE re-derives every conformance claim from committed bytes alone and
  never accepts the author's self-report, with the hardness contract deliberately hidden from it so
  it grades the bytes rather than the intent.

Current dispositions: FORGE `HOLD:PILOT_REQUIRED`, CRUCIBLE `HOLD`, ENGRAM ledger `STALE`. Nothing
reaches `SHIP` without an external signed pilot over frozen bytes.

## Limitations

Named limitations, each paired with the work that closes it.

- **One curated task.** 1 of 30 tasks is authored and frozen, so this sample demonstrates the
  format, the verifier, and the gate, and measures nothing about model capability. **Next.** Author
  tasks 02 to 30 against the cohort plan, each through the full FORGE gate.
- **No cohort pass rate.** With n=1 there is no rate to report, and none is projected.
  **Next.** Publish per-level rates once the cohort is authored and a signed pilot lands.
- **Difficulty is unproven, by construction.** Every hardness claim in the cohort plan is a design
  intent anchored to a published lever, not evidence. A task that no model has failed under a signed
  pilot is unproven, never hard. **Next.** Run the external pilot against the frozen solver registry
  under the predeclared statistical rule (n=30 rollouts, one-sided 95% Clopper-Pearson, frontier
  defeat floor 0.05).
- **The pilot CI is not yet bound.** Terra has no external signed pilot CI wired, tracked as coverage
  gap G13, so the frontier-defeat gate cannot fire. **Next.** Bind the external signer and land the
  first signed CFER envelope.
- **Contamination screen is per-bundle only.** Canary tokens are tracked per bundle; there is no
  corpus-snapshot binding. Whether a pinned public source appeared in a model's training data is
  unknown. **Next.** Bind a corpus-snapshot digest once an authoritative pretraining-corpus
  reference is available.

## Links

- **Knowledge repo:** https://github.com/EtharaOrion/terra
- **Harness:** https://github.com/EtharaOrion/terra-harness
- **Dataset:** https://github.com/EtharaOrion/terra-dataset
- **Trajectories:** https://github.com/EtharaOrion/terra-trajectories
- **Delivery:** https://github.com/EtharaOrion/terra-delivery
- **Contracts (Trinity):** https://github.com/Ethara-Ai/trinity
- **Dashboard:** https://projects.ethara.ai/terra
- **Method (GAIA):** https://arxiv.org/abs/2311.12983

## License

Released under the MIT License (see [LICENSE](LICENSE)). Task bundles produced through this project
retain their own original licences.

---

Terra · Ethara.AI · Sample document version 0.3 · Last updated 2026-08-03
