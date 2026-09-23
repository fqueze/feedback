## `test` calls a skipped-since config a perma-fail

- Command: `fx-tests test widget/tests/test_clipboard_getDataSnapshot.html` (data 2026-09-01 … 2026-09-21).
- Expected: the verdict to say the 4 android-em-14-x86_64-ccov configs failed every run from 2026-09-08 to 2026-09-15 and have been skipped there since (the `SKIP ... ccov` row, 70x, is the same configs).
- Got: `Verdict: perma-fail. Never passed on 4 configurations`, and the `recent` column still 100% — nothing on that row says those configs stopped running the test six days ago.
- Cost: had to read `--history` and the Issues list side by side to see the failures end on 09-15 and the ccov skip start, before knowing the Kind was `no longer fails`.
- Workaround: `--history`, then match the new SKIP row to the failing configs by hand. A "last run / last failure" date per failing config would answer it.
