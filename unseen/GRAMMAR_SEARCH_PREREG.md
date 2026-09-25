# RSI GRAMMAR-GAP SEARCH — PRE-REGISTRATION (frozen before execution)
Date: 2026-09-23
Purpose: Count genuine grammar-gap failures in a pre-registered sample of never-touched
clean django instances, to find an eligible target for the EXISTING capability_cycle.
This is a SEARCH for a verdict type, NOT a capability/repair claim.

## CANDIDATE POOL (frozen)
145 clean django instances, never development-touched.
Source: clean_pool_final.json (BENCH_1 contamination-audited) MINUS all exposed
(frozen BENCH_1/2 fifteen + every prediction-file-touched django = 85 excluded).
Pool file: runs/rsi/candidate_pool_raw.json  |  pool sha256[:16]: 88866e63cfe5fa76

## SELECTION (deterministic, frozen)
Sort the 145 by instance_id ascending; take the FIRST 30.
The selected 30:
  django__django-10554
  django__django-10914
  django__django-10973
  django__django-11066
  django__django-11119
  django__django-11133
  django__django-11138
  django__django-11141
  django__django-11163
  django__django-11179
  django__django-11206
  django__django-11239
  django__django-11265
  django__django-11276
  django__django-11299
  django__django-11333
  django__django-11400
  django__django-11477
  django__django-11490
  django__django-11551
  django__django-11555
  django__django-11603
  django__django-11734
  django__django-11790
  django__django-11820
  django__django-11848
  django__django-11885
  django__django-11951
  django__django-11999
  django__django-12050

## N = 30. No expansion under any outcome.

## ELIGIBILITY (existing bar, UNCHANGED)
An instance is an ELIGIBLE GRAMMAR GAP iff BOTH:
  (a) loop verdict == GRAMMAR_EXHAUSTED_AT_WALKED_TARGETS, AND
  (b) the unchanged gap_precondition() returns PASS (COMPLETED measurements,
      reached synthesis, target executed).
gap_precondition is NOT modified. No relabeling. No reinterpretation of verdicts.

## RUN CONFIG (frozen = BENCH_2)
Tool mode; --max-attempts 1 --max-steps 20 --max-calls 20 --timeout 1800.
Timeout 1800s deliberately (a shorter timeout could cut synthesis short of genuine
grammar exhaustion and artificially reduce k). One attempt per instance.

## STOPPING RULE
Run exactly the 30 selected. No early stop, no expansion, no cherry-picking, no
instance swapping, regardless of interim outcomes.

## OUTCOME REPORTING
Report k/30 eligible grammar gaps, each instance's verdict listed.
  - If k > 0: freeze the FIRST eligible case by instance_id -> run EXISTING
    capability_cycle UNCHANGED -> gate -> human approval -> harness verification
    -> pre-registered held-out transfer (that step gets its OWN pre-registration).
  - If k == 0: report "0/30 eligible grammar gaps in the pre-registered sample."
    Evidence the trigger is narrow -- NOT "RSI failed."

## INVARIANTS
- gap_precondition and capability_cycle: UNCHANGED throughout this experiment.
- BENCH_2 frozen result (0/9 valid unseen-eligible) is untouched; this search is
  separate and does not alter it.
- No infrastructure-cycle work here (later, separate, failure-agnostic build; astropy
  held out, not designed toward).

## EVIDENCE BOUNDARY (unchanged)
- Repair: 5/5 development/in-sample, harness-confirmed.
- Unseen BENCH_2: 0/9 valid unseen-eligible resolved.
- RSI: mechanism exists and is correctly gated; real unseen execution/success not yet
  demonstrated. This search does not advance the RSI claim unless k>0 AND the cycle
  runs unchanged and succeeds through gate + harness + transfer.
