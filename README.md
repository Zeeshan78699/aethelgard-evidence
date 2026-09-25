# Aethelgard — Evidence Record

Independent verification of Aethelgard-generated patches via the official SWE-bench harness.

**Last updated: 2026-09-25.** This record supersedes all earlier versions in this repository's history. Earlier commits reported higher repair counts (e.g. "5 in-sample + 3 held-out repairs") that per-patch inspection did not support. The honest, inspected result is below.

---

## 1. Result

Eight Aethelgard-generated patches pass the official SWE-bench harness — their FAIL_TO_PASS and PASS_TO_PASS tests pass, graded by the maintainers' `swebench` package, independent of Aethelgard's own verifier.

**Passing the tests is not the same as genuinely fixing the bug.** Every harness-passing patch was then inspected with `check_resolution` (AST comparison against the maintainers' fix) and by hand. The result is **4 genuine repairs and 4 test-passing artifacts.**

### Genuine repairs — 4

**Gold-match (3)** — identical AST to the maintainers' fix:

| Instance | Repository |
|---|---|
| astropy__astropy-12907 | astropy/astropy |
| astropy__astropy-13236 | astropy/astropy |
| astropy__astropy-14539 | astropy/astropy |

**Verified functional equivalent (1)** — differs from the maintainers' fix in code, but proven to produce the same correct behaviour by tests that specifically check it:

- **django__django-14559** — returns the row count from `bulk_update()`. Not gold-match (different code from the maintainers' fix), but the FAIL_TO_PASS tests assert the returned count across multiple batches (2000 rows; and duplicates with `batch_size=1`), and this patch passes all of them — so its accumulation across batches is verified correct, not merely assumed.

### Test-passing artifacts — 4 (retracted)

These pass the tests without genuinely fixing the bug: they delete, short-circuit, or override the responsible code, so the narrow test passes while the underlying bug (or normal behaviour) is not correctly handled.

- **astropy__astropy-14309** (in-sample) — the *published* patch is a test-passing artifact: it bails out on falsy `origin` (`if not origin: return None`), leaving the described `IndexError` for truthy origin. **However**, on re-running the frozen system five fresh times, it produced the maintainers' GOLD-MATCH fix in all 5 runs — so the system reliably produces the genuine fix on this instance; the single published run was an unlucky artifact draw. The re-runs post-date knowledge of the gold patch, so they are reported as a reliability observation, not as a claimed resolved result. The published patch stands in the record as the artifact.
- **django__django-11179** (held-out pool) — deletes the fast-delete optimization branch instead of setting the PK to `None`. On 5 fresh re-runs the system produced the identical deletion every time (5/5, 0 additions) — a stable artifact, not an unlucky draw.
- **django__django-11265** (held-out pool) — deletes the `MultiJoin` raise instead of propagating `_filtered_relations` into the subquery.
- **django__django-11299** (held-out pool) — makes `_get_col` return `SimpleCol` unconditionally (the real branch becomes dead code), globally disabling column qualification.

---

## 2. Scope — stated plainly

- All 8 patches are harness-passing. **Only the 4 genuine repairs (3 gold-match + 1 verified functional equivalent) are claimed as repairs.**
- The 4 genuine repairs are **in-sample** (development set).
- astropy-14309 (in-sample) and django-11179/11265/11299 (drawn from a held-out pool) are harness-passing but, on inspection, **test-passing artifacts** — retracted as repairs.
- **No unseen-generalization claim** and **no recursive-self-improvement claim** is made. Neither is established by this evidence. Earlier text in this repository's history that framed the three held-out artifacts as "unseen repairs" is withdrawn: they pass the tests but do not fix the bugs.

---

## 3. Key finding

**Harness-"RESOLVED" means the tests pass; it does not mean the bug was genuinely fixed.** A patch can satisfy a narrow FAIL_TO_PASS test by removing or short-circuiting the responsible code. The reliable bars are: AST comparison to the maintainers' fix (GOLD-MATCH), or — where the code differs — passing tests that specifically exercise the behaviour in question (verified functional equivalent, as with django-14559). A single test-pass is not sufficient.

A related observation from re-running the frozen system: **its synthesis is non-deterministic.** On some instances (astropy-14309) fresh re-runs reliably produce the maintainers' gold-match fix (5/5); on others (django-11179) fresh re-runs reliably reproduce the same artifact (5/5 identical deletions). A single published run can therefore capture either a genuine fix or an artifact — which is why per-patch inspection, and repeated runs, are required.

---

## 4. Reproduce

Requires Docker and Python 3.10+ (Linux/WSL2 — the harness uses the Unix `resource` module and `X | None` syntax).

```
pip install swebench            # this record used swebench 4.1.0

python -m swebench.harness.run_evaluation \
  --dataset_name princeton-nlp/SWE-bench_Verified \
  --predictions_path aethelgard_5_predictions.jsonl \
  --run_id verify --max_workers 4
```

The harness will report submitted patches as RESOLVED (their tests pass). To separate genuine repairs from artifacts, compare each patch's edited function against the maintainers' fix (`check_resolution` performs this AST comparison); the 3 gold-match are identical to gold, django-14559 is a verified functional equivalent, and the 4 artifacts pass the tests without fixing the bug.

**Environment of record:** swebench 4.1.0; dataset `princeton-nlp/SWE-bench_Verified`, revision `c104f840cc67f8b6eec6f759ebc8b2693d585d4a`; evaluation via Docker.

---

## 5. Files

- `aethelgard_5_predictions.jsonl` — the in-sample patches (astropy-12907, 13236, 14309, 14539, django-14559) in harness format. Of these, 12907/13236/14539 are GOLD-MATCH; 14559 is a verified functional equivalent; 14309's published patch is an artifact (see the note above on its re-run behaviour).
- `unseen/rsi_unseen_3_predictions.jsonl` — the three held-out-pool patches (11179, 11265, 11299), all test-passing artifacts on inspection.
- `unseen/GRAMMAR_SEARCH_PREREG.md` — the pre-registration for the held-out sampling (sha256 `20b3ea270c92ce1c2223a31dfe9ebc3f3886bfab4184a04d62dc441b8fd3d3d9`).
- Harness reports for the runs are retained in the repository.

---

## 6. Integrity note

The harness reports are the maintainers' output, independent of Aethelgard. The prediction files contain the exact patches evaluated, so any reader can re-run the harness and obtain the same RESOLVED verdicts — and can inspect the patches to confirm the genuine/artifact split above. This record makes no claim that cannot be checked against those files plus the public `swebench` package and the SWE-bench_Verified dataset.

---

## Honest bottom line

**4 genuine repairs: astropy-12907, astropy-13236, astropy-14539 (gold-match, identical to the maintainers' fixes) and django-14559 (verified functional equivalent — different code, proven correct on the multi-batch return-value tests).** All in-sample, harness-passing.

For astropy-14309, the published patch is an artifact, but the system reliably produces the gold-match fix on re-run (5/5) — reported honestly as a reliability observation, not a claimed result. django-11179/11265/11299 are test-passing artifacts. Test-pass is not genuine repair; gold-match or behaviour-verified equivalence is the bar this record holds.
