# Aethelgard — Evidence Record

**Independent verification of Aethelgard-generated patches via the official SWE-bench harness, and the state of the project's benchmark work.**

Date: 20 September 2026
Purpose: a factual, self-contained record of what has been independently verified, what remains unverified, and exactly how each claim can be re-checked by a third party. Every claim here is either backed by a re-runnable artifact or explicitly marked as not yet established.

**Project objective:** Aethelgard is a research project whose central aim is bounded, gated, reversible recursive self-improvement for autonomous software repair. This record documents the progress independently confirmed to date; further objectives (unseen-bug generalization and recursive self-improvement on real instances) are in progress and will be published as they are proven.

---

## 1. Headline result (independently verified)

**Five Aethelgard-generated patches were confirmed by the official SWE-bench evaluation harness.**

| Instance | Repository | Local mechanism | Harness verdict |
|---|---|---|---|
| astropy__astropy-12907 | astropy/astropy | name substitution | RESOLVED |
| astropy__astropy-13236 | astropy/astropy | branch removal | RESOLVED |
| astropy__astropy-14309 | astropy/astropy | guard→return | RESOLVED |
| astropy__astropy-14539 | astropy/astropy | clause insertion | RESOLVED |
| django__django-14559 | django/django | guard → return | RESOLVED |

**Harness result:** 5 submitted, **5 resolved, 0 unresolved, 0 errors.**

This was produced by the **maintainers' own code** (the `swebench` package), not by Aethelgard. It is therefore independent of Aethelgard's internal verifier.

### 1a. Patch provenance

The harness verifies that the submitted patches resolve their instances; it does not by itself establish who generated them. Provenance for each patch:

- Each patch was produced by an Aethelgard run and written to a prediction file (`predictions_<id>_*.jsonl`) at run time. Source files (latest per instance): astropy-12907 → `predictions_12907_20260826_115645.jsonl`; astropy-13236 → `predictions_13236_20260824_144947.jsonl`; astropy-14309 → `predictions_14309_20260918_100206.jsonl`; astropy-14539 → `predictions_14539_20260824_153515.jsonl`; django-14559 → `predictions_14559_20260918_085225.jsonl`.
- The **mechanism column** in the table above is itself provenance: each patch's shape corresponds to a specific rule in Aethelgard's deterministic edit grammar (name substitution, branch removal, guard→return, clause insertion) — the form a grammar-generated patch takes, distinct from an arbitrary hand-written fix.
- The corresponding Aethelgard run logs (`log_<id>_*.txt`) and the six-surface contamination audit trail are retained and available.
- Provenance note: run logs are the author's own records; they evidence that Aethelgard's pipeline produced the patches, but are not independent third-party attestation. The independent element is the harness verdict on the patches' correctness.

## 2. Scope of this result

These five instances were part of Aethelgard's development set (in-sample). What that means for reading the result:

- Their independent harness confirmation validates that the **patches are genuinely correct** — it closes the gap between "Aethelgard's own verifier says VERIFIED" and "an independent judge agrees."
- It is a confirmation of patch correctness, not yet a measure of performance on **unseen** bugs.

**Note — work in progress:** testing on unseen instances is underway (PROTOCOL_BENCH_2, a frozen protocol described in Section 6). When that run is complete and its patches are independently confirmed by the same harness, those results will be published as the measure of generalization. This record covers only what is confirmed today; the unseen-bug results will be added when proven.

## 3. How to reproduce the verification (anyone can do this)

The verification uses the official harness. It requires Docker and Python 3.10+ on Linux/WSL2 (the harness imports the Unix-only `resource` module and uses `X | None` type syntax, so it does not run on Windows Python or Python 3.9).

```bash
# 1. environment (Python 3.10+; here 3.14)
python3.14 -m venv swebench-venv
source swebench-venv/bin/activate
pip install swebench            # this record used swebench 4.1.0

# 2. confirm the harness itself works, using a GOLD patch (maintainers' known-correct fix)
python -m swebench.harness.run_evaluation \
  --dataset_name princeton-nlp/SWE-bench_Verified \
  --predictions_path gold \
  --instance_ids astropy__astropy-14309 \
  --run_id gold_check --max_workers 1
#   -> expect: 1 resolved. This proves Docker + harness + dataset are wired correctly.

# 3. verify Aethelgard's own patches
python -m swebench.harness.run_evaluation \
  --dataset_name princeton-nlp/SWE-bench_Verified \
  --predictions_path aethelgard_5_predictions.jsonl \
  --run_id aethelgard_5_verify --max_workers 4
#   -> report written to aethelgard.aethelgard_5_verify.json
#      read resolved_ids for the independent verdict.
```

**Environment of record (for exact reproduction):**
- swebench package: **4.1.0**; report `schema_version`: **2**; evaluation: Docker
- dataset: **princeton-nlp/SWE-bench_Verified**, revision **c104f840cc67f8b6eec6f759ebc8b2693d585d4a**
- `aethelgard_5_predictions.jsonl` SHA-256: **5f3af0be78bbfacc8b6898cf3be38d6e5ce1f8e0f33fb38ce400f4a447918359**
- `HARNESS_CONFIRMED_5_20260920.json` SHA-256: **624162a0437c5d54009fff8e8ae4bfc83862ab7f72944233d45d1aad6558f646**
- resolved_ids (from the report): astropy-12907, astropy-13236, astropy-14309, astropy-14539, django-14559 — **5 of 5**

**Artifacts that accompany this record:**
- `aethelgard_5_predictions.jsonl` — the five patches, in the harness's required format (`instance_id`, `model_name_or_path`, `model_patch`).
- `HARNESS_CONFIRMED_5_20260920.json` — the harness report (`resolved_ids` lists all five).
- `gold.gold_check.json` — the gold-patch check proving the harness works.

## 4. The prior four astropy results, now confirmed

Before this verification, the project reported four astropy instances as "GOLD-MATCH" by Aethelgard's own verifier, and django-14559 as "RESOLVED (PASSES BUT DIFFERS)" — none independently harness-confirmed. The verification in Section 1 changes that status:

- **Independently harness-confirmed (Aethelgard's patches): 0 → 5.**

The distinction that held throughout the project:
- **Aethelgard `VERIFIED`** = the system's own check (internally, the `VERIFIED_LOCAL_ONLY` category).
- **`swebench.harness` `resolved`** = the independent judge. Only this is officially RESOLVED.

## 5. What is NOT yet established

Stated plainly, so the record cannot be read as overclaiming:

1. **Unseen-bug benchmark — in progress, not yet published.** The five confirmed instances are in-sample. A run on unseen instances (PROTOCOL_BENCH_2) is being prepared; its harness-confirmed results will be published when complete.
2. **Recursive self-improvement — a core project objective, in progress.** Bounded, gated recursive self-improvement is the central aim of Aethelgard. The self-learning mechanism has been demonstrated on a controlled instance; extending and proving it on real instances is ongoing, and those results will be published when independently confirmed. This record covers the harness-confirmed patch results (Section 1); the recursive self-improvement results will be added as they are proven.
3. **No independent third-party reproduction.** The harness confirms the patches on the author's machine. A separate party running the same command would strengthen this further; the artifacts in Section 3 make that possible.

## 6. Benchmark protocol status (context)

A frozen benchmark protocol (`PROTOCOL_BENCH_2`) exists for a future unseen-instance run. Its state, for completeness:

- A frozen set of 15 instances (9 django + 6 astropy). Of these, **13 are held unseen** — mechanically selected from an evidence-grounded clean pool, six-surface contamination-audited (development ledger, attribution records, operator files, run logs, gold-loading records, prediction files) before freezing.
- **The remaining 2 (django-13512 and django-13809) were exposed during pipeline fix-development** and are NOT unseen. When results are reported, these two will be reported separately from the 13 unseen instances, subject to the contamination audit — never blended into the unseen count.
- The pipeline was corrected and regression-tested (8 test suites green) after a set of defects and a stale-base regression were found and fixed. Deployed component hashes are recorded in the freeze block.
- **This run has not yet been executed.** When it is, its predictions will be verified through the identical harness process (Section 3), and the headline figure will be stated as "N of 13 unseen (harness-confirmed)", with the 2 development-exposed instances disclosed separately.

## 7. Exact claims permitted by this evidence

**May be stated (backed by the artifacts here):**
- "Five Aethelgard-generated patches are independently confirmed by the official SWE-bench harness (swebench 4.1.0): astropy-12907, 13236, 14309, 14539, and django-14559."
- "The harness verification is reproducible from the published predictions and report."

**Scope note to include:**
- "These five instances are in-sample; this confirms patch correctness. Unseen-bug testing is in progress and will be published when proven."

**May NOT be stated:**
- Any unseen-benchmark score, generalization claim, or "recursive self-improvement" — none is established by this evidence.

## 8. Integrity note

The harness report is the maintainers' output and is independent of Aethelgard. The predictions file contains the exact patches evaluated. Together they let any reader re-run the evaluation and obtain the same `resolved_ids`. This record makes no claim that cannot be checked against those two files plus the public `swebench` package.

---

*Prepared as a re-verifiable evidence record. The five-instance harness confirmation is real and independent. Unseen-bug results are in progress and will be published when independently confirmed.*
