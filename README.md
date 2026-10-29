# DSS5105 Track 2 — B (Evaluation lead) deliverables

Backup of the Evaluation-lead (B) deliverables for the Quick-Response Subcontracting
Desk capstone, Group 22. Each item below is also available as a git patch against the
team repository at `github.com/kailinghew/DSS5105_Project`.

## What is in here

| Task | Files |
|---|---|
| B1 · gold-label cross-check | `B1_gold_label_crosscheck.md` (30/30 verified, disputed cases, resolution), `B1_gold_labels_R01.patch`, `0001-B1-*.patch` |
| B2 · frozen held-out set | `heldout.txt`, `heldout_labels.json`, `0002-B2-*.patch` (includes the `--file` / `--labels` option for `run_inbox.py`) |
| B3 · stricter number-trace check | `0001-B3-*.patch` (role-based `untraced_numbers()` in `desk/explain.py` + `tests/test_explain.py`) |
| B4 · failure log | `failures.md` (9 reproduced cases + 2 resolved), `0001-B4-*.patch` |
| B5 · validation slide | `B5_validation_slide.md` (slide copy + ~40s script), applied in the deck below |
| Sprint 2 deck | `Track 2 Sprint 2 Review_updated.pptx` (slide 6 progress updated, slide 8 validation plan + results) |

## Applying the patches to the team repo

The patches apply in order onto commit `e5b3590` (`deploy render`) of the team repo:

```bash
git am 0001-B1-tighten-R01-gold-label-with-matches-2-agreed-with.patch
git am 0002-B2-freeze-held-out-set-2026-10-26-add-file-labels-to.patch
git am 0001-B3-number-trace-check-matches-each-number-to-the-fac.patch
git am 0001-B4-start-the-failure-log-9-reproduced-cases-2-resolv.patch
```

## Notes

- The held-out set is sealed: it is only read once, by A, for the single final Claude
  run (`python -m eval.compare_parsers --file eval/heldout.txt --labels
  eval/heldout_labels.json --name heldout`). Keep it out of tuning.
- All numbers in `B5_validation_slide.md` come from the frozen code
  (`agent/CHANGELOG.md`, `eval/results_sim.csv`, held-out `run_inbox` run).
