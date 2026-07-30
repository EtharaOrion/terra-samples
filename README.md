<p align="center">
  <img src="assets/hero.jpeg" alt="Terra: real-world, multi-step, multi-modal tasks for general AI assistants, scored by a verifiable exact-match reward" width="880">
</p>

<p align="center">
  <strong>Measuring general-assistant competence on real-world, multi-step, multi-modal tasks, scored by a verifiable exact-match reward.</strong>
</p>

<p align="center">
  <a href="#summary"><img alt="Built by Ethara.AI" src="https://img.shields.io/badge/built%20by-Ethara.AI-2e7d4f.svg"></a>
  <a href="#scoring-methodology"><img alt="Scoring: binary, exact match" src="https://img.shields.io/badge/scoring-binary_·_exact_match-3fa06f.svg"></a>
  <a href="#scoring-methodology"><img alt="Reward: no judge in the loop" src="https://img.shields.io/badge/reward-no_judge_in_the_loop-845EF7.svg"></a>
  <a href="#verification-and-quality-assurance"><img alt="Reward: verifiable, never estimated" src="https://img.shields.io/badge/reward-verifiable%2C_never_estimated-ff6b6b.svg"></a>
</p>

<p align="center"><sub>
  <a href="#summary">Summary</a> · <a href="#repository-layout">Layout</a> · <a href="#difficulty-levels">Levels</a> · <a href="#results-pass-rate-by-level">Results</a> · <a href="#analysis">Analysis</a> · <a href="#coverage">Coverage</a> · <a href="#dataset-structure">Dataset</a> · <a href="#trajectory-structure">Trajectories</a> · <a href="#scoring-methodology">Scoring</a> · <a href="#reproduction">Reproduction</a> · <a href="#verification-and-quality-assurance">Verification</a>
</sub></p>

# Terra: 20-Task Evaluation Sample

**Terra measures whether an agent can complete real-world assistant tasks, not just answer trivia.**
Each task is a question that is simple for a capable human but hard for current models: it takes
several steps and more than one modality to reach the answer, coordinating web browsing, document
and file reading (PDF, spreadsheet, CSV), image and video understanding, and multi-step
quantitative reasoning. Every task resolves to a single unambiguous answer that a grader checks by
**exact match**, so the reward is a clean binary signal with no judge model in the loop.

This is a curated **20-task** validation sample from Terra. Each task is a self-contained,
multi-step question over real documents, paired with the complete agent trajectories of the pilot
cohort (**Kimi K2.5** and **Qwen3-VL**) at 1 run per model, for **40 graded runs** in total, each
scored by the Terra exact-match verifier.

Tasks are grouped into three difficulty levels (Level 1, 2, 3, mapping to Intermediate, Advanced,
Expert), assigned at authoring time from the number of reasoning and tool steps a correct solution
requires, and span six attachment modalities (image, video, spreadsheet, CSV, PDF, and text only).

Accuracy declines sharply as the level rises, and neither pilot model solves a single Level 3 task.
See [Results](#results-pass-rate-by-level).

> **This is a representative, quality-controlled sample of the full Terra corpus,** provided for
> evaluation. The dataset format, trajectory format, and scoring are identical to the production
> benchmark.

## Summary

| Property         | Value                                                                        |
| :--------------- | :--------------------------------------------------------------------------- |
| Tasks            | **20** (Level 1: 4 / Level 2: 7 / Level 3: 9)                                |
| Difficulty levels | 3, assigned at authoring time from required reasoning and tool steps        |
| Models evaluated | Kimi K2.5, Qwen3-VL                                                           |
| Runs and grid    | 1 per model (`kimi-k2.5` / `qwen3-vl`) = 2 per task, 40 total; full 2 × 1     |
| Score            | binary exact match in {0, 1}, per run in `summary.json` (`score`)            |
| Modalities       | image, video, spreadsheet, CSV, PDF, text only                              |
| Task categories  | web browsing, file reading, multi-modality, calculation, self-contained     |

**Pass rates on this sample** (pass@1, the fraction of a model's runs that scored an exact match):

| Metric                | Value          |
| :-------------------- | -------------: |
| Kimi K2.5 **pass@1**  | 50.0% (10/20)  |
| Qwen3-VL **pass@1**   | 10.0% (2/20)   |

> ### Frontier cohort: `PENDING PILOT`
>
> The next evaluation cohort is **GPT-5.6-Sol** and **Claude Opus 4.8**. Those runs are **not yet
> complete**: no `gpt-5.6-sol` or `opus-4.8` trajectories exist in `trajectories/` at the time of
> writing, so **no frontier numbers are published here.** In keeping with the *verifiable, never
> estimated* stance, this stays a fail-closed placeholder, not a projection. It will be populated
> from real scored trajectories once the runs land, and the live
> [dashboard](https://projects.ethara.ai/terra) updated in lockstep.

## Repository layout

```
terra-samples/
├── README.md                 # this document
├── assets/                   # hero banner
│   └── hero.jpeg
├── dataset/                  # task definitions: dataset.jsonl + file_attachments/, keyed by UUID (20)
│   ├── dataset.jsonl
│   └── file_attachments/<uuid>.<ext>
└── trajectories/             # model runs, one directory per UUID (20)
    └── <uuid>/<model>/ ...    # model ∈ {kimi-k2.5, qwen3-vl}
```

Task UUIDs match **1:1** between `dataset/` and `trajectories/` (20 each). Under each task, both
models appear as `<model>/` directories, each containing exactly one run.

## Difficulty levels

The 20 tasks are stratified into three levels. Unlike an outcome-based split, a Terra level is fixed
**at authoring time** from the number of reasoning and tool steps a correct solution requires: Level
1 tasks are short, mostly single-tool lookups; Level 3 tasks chain many steps across several
modalities. The level is a property of the task, decided before any model runs, so it describes the
task rather than what a given model happened to experience.

| Level (tier)          |   n | Kimi K2.5 accuracy | Qwen3-VL accuracy |
| :-------------------- | --: | -----------------: | ----------------: |
| **Level 1** (Intermediate) |   4 |            100.0% |            25.0% |
| **Level 2** (Advanced)     |   7 |             85.7% |            14.3% |
| **Level 3** (Expert)       |   9 |              0.0% |             0.0% |

## Results: pass rate by level

Both pilot models degrade monotonically as the level rises, and both bottom out at 0% on Level 3.
Per-task scores are derivable directly from the shipped files: the level from
`dataset/dataset.jsonl` (`Level`) and each per-run outcome from
`trajectories/<uuid>/<model>/summary.json` (`score`).

**Per-level pass@1 by model** (fraction of each model's runs in the level that scored an exact match):

| Level (n)    | Kimi K2.5 | Qwen3-VL |
| :----------- | --------: | -------: |
| Level 1 (4)  |    100.0% |    25.0% |
| Level 2 (7)  |     85.7% |    14.3% |
| Level 3 (9)  |      0.0% |     0.0% |
| **Overall (20)** | **50.0%** | **10.0%** |

With 20 tasks across three levels at one run per model, these are an average tendency on a small,
curated sample rather than a precise law. They exist to sanity-check difficulty calibration and the
exact-match reward oracle before the corpus is scaled.

Inference cost is comparable between the two pilot models, while their accuracy is not: Kimi K2.5
reaches 50% at about the same per-task cost at which Qwen3-VL reaches 10%.

## Analysis

Terra is built to keep partial-looking progress from masquerading as success. Because grading is
exact match against a single frozen answer, a run either produces that answer or it does not: there
is no credit for a well-reasoned trajectory that lands on the wrong number. That makes the level
curve blunt and honest. Both models clear the short Level 1 tasks (Kimi 100%, Qwen 25%), diverge
through Level 2 (85.7% vs 14.3%), and then both collapse to 0% on the multi-hop, multi-modal Level 3
tail. The place where current agents break is not single-tool lookups but long chains that must hold
several modalities and intermediate results together without a slip.

The two pilot models separate cleanly by accuracy while costing about the same per task, so the
gap is a capability gap rather than a budget gap. Kimi K2.5 solves five times as many tasks as
Qwen3-VL (10 vs 2) at a similar price, which is exactly the kind of separation an exact-match
environment is designed to expose: no judge model softens the boundary, and no partial credit
narrows it.

These separations are trustworthy only because the scores cannot be gamed. The reward is computed
from the agent's own recorded final answer against a ground truth it never sees, missing or
unparseable answers fail closed as incorrect, and any run that edited the graded answer or reached
for a reference solution is rejected rather than scored. A model's standing reflects the task it
solved, not an answer it found.

## Coverage

The 20 tasks span six attachment modalities. Image tasks are the most common; five tasks are
text only (answerable from reasoning and web tools alone, with no attached file).

| Modality        | Tasks |     | Modality   | Tasks |
| :-------------- | ----: | :-- | :--------- | ----: |
| Image (png)     |     8 |     | CSV        |     2 |
| Text only       |     5 |     | Spreadsheet (xlsx) | 1 |
| Video (mp4)     |     3 |     | PDF        |     1 |

Each task also carries a task category (web browsing, file reading, multi-modality, calculation, or
self-contained) describing the dominant skill it exercises.

## Dataset structure

The task definitions live under `dataset/`:

```
dataset/
├── dataset.jsonl             # one JSON record per task (20 lines)
└── file_attachments/
    └── <uuid>.<ext>          # the real attached artifact (png, mp4, xlsx, csv, pdf)
```

Each `dataset.jsonl` record has: `task_id` (UUID), `Question` (the prompt), `Level` (1 to 3),
`Final answer` (the exact-match ground truth), `file_name` and `file_path` (the attached artifact,
empty for text-only tasks), and an `Annotator Metadata` block (`Steps`, `Number of steps`, `How long
did this take?`, `Tools`, `Number of tools`). During a run the agent is shown only the `Question` and
the attachment; `Final answer` is used exclusively by the verifier and is never exposed to the agent.

## Trajectory structure

Each run lives under `trajectories/<uuid>/<model>/`:

```
trajectories/<uuid>/<model>/            # model ∈ {kimi-k2.5, qwen3-vl}
├── summary.json            # the graded result: score, model_answer, ground_truth, cost, tokens, latency
├── metadata.json           # run configuration and environment
├── output.jsonl            # the full step-by-step agent trace (actions + observations)
├── output.critic_attempt_*.jsonl   # critic re-check passes over the final answer
└── conversations/          # per-turn message logs
```

Key `summary.json` fields: `score` (boolean exact-match reward), `model_answer` and `ground_truth`
(what the agent produced versus the frozen answer), `cost` (USD), `prompt_tokens`,
`completion_tokens`, `llm_calls`, and `avg_latency_seconds`. The model is identified by its
directory name (`kimi-k2.5` or `qwen3-vl`).

## Scoring methodology

The verifier assigns each run a binary score:

```
score = 1  if normalize(model_answer) == normalize(ground_truth)  else  0
```

Normalization is deterministic (trimming, casing, and numeric/format canonicalization) and there is
**no judge model in the reward path**. A run solves a task only when its `score` is exactly 1
(`true` in `summary.json`). Throughout this document, pass@1 is a run-level rate: a model's solved
runs divided by its total runs. Missing or unparseable answers **fail closed** as incorrect, never
skipped.

Every task ships a human-authored ground truth (`Final answer`) that a capable human reached from
the question and its attachment. This guarantees the grading harness is self-consistent (each task
has a known-correct answer under exact match); it is a curation guarantee, **not** an independent
measure of real-world solvability.

## Reproduction

Agent execution and scoring are orchestrated by the **Terra harness**. The score and full
diagnostics for every run are in `trajectories/<uuid>/<model>/summary.json`.

### Recompute the pass rates yourself

No trajectory re-execution is needed. Read the shipped scores directly:

```python
import json, glob, collections

def solved(run_dir):                      # a run "solves" iff score is true
    s = json.load(open(f"{run_dir}/summary.json"))
    return bool(s["score"])

# pass@1 per model = fraction of that model's runs that solved
by_model = collections.defaultdict(list)
for sm in glob.glob("trajectories/*/*/summary.json"):
    model = sm.split("/")[-2]
    by_model[model].append(solved(sm.rsplit("/", 1)[0]))

for model, runs in sorted(by_model.items()):
    print(f"{model} pass@1 = {sum(runs) / len(runs):.3f}")
# -> kimi-k2.5 0.500 · qwen3-vl 0.100
```

Per-level rates come from joining each run's `score` to its task `Level` in `dataset/dataset.jsonl`.

## Verification and quality assurance

This sample passed a multi-level QC gate prior to delivery. The dataset is validated with a QC
toolkit and a QC prompt, combining a deterministic audit, a non-deterministic audit, and independent
human review.

- **Structure.** 20 dataset records and 20 trajectory directories, matched 1:1 by UUID; both models
  present under every task; required files present and non-empty in every run; the full 2 × 1 grid
  (40 runs) is complete.
- **Deterministic audit (`qc_toolkit`).** Every required field is present and well-formed
  (`task_id`, `Question`, `Level` in 1 to 3, a non-empty `Final answer`, matching `file_name` and
  `file_path`); each attachment resolves, opens, and is the declared type; and every `score` in
  `summary.json` reconciles against `model_answer` versus `ground_truth` under exact match.
- **Non-deterministic audit (QC prompt).** A review checks what a script cannot: that each question
  is well-posed with one unambiguous answer, that the answer is derivable from the question, its
  attachment, and public sources, that the annotator metadata (steps, tools, counts) is coherent,
  and that every attachment is legible rather than blurred, cropped, or corrupt.
- **Human validation.** An independent reviewer works a fixed acceptance checklist, spot-solving
  tasks to confirm the answer is unique and correct and verifying attachment quality and metadata.
- **Reward-hacking resistance.** The agent never sees the ground truth; any run that edited the
  graded answer or reached for a reference solution is rejected rather than scored. Missing or
  unparseable answers fail closed as incorrect.
- **Limitations.**
  - **Sample size.** 20 tasks (4 to 9 per level) at one run per model; per-level breakdowns are
    averages on small n with real run-to-run variance, not precise estimates.
  - **Single run.** One run per model, so a model's result on a given task reflects a single draw
    rather than a distribution.
  - **Contamination.** Tasks draw on public sources; whether any appeared in a model's training data
    is unknown, so pass rates are not a contamination-free measure of difficulty.
