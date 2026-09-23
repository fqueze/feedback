## Which check site failed, when two share a message (test_animations_omta_scroll.html)

- Question: which of the two identical `ok()` calls (file_animations_omta_scroll.html:200 vs :254) produced "scroll animation in delay phase gets the OMTA style ..." in each failing run?
- Command: `fx-tests test layout/style/test/test_animations_omta_scroll.html --task-ids --limit 0`
- Got: one Issue row, 78x, no stack; the two sites are indistinguishable.
- Workaround: downloaded 13 live_backing.log files and grepped the first stack frame after the failure (8 at :254, 5 at :200). Grouping Issues by message + first test-file stack frame, or showing it, would have answered it.

## Runs and failures per platform

- Question: how many runs and failures per platform (Windows had 0 failures: out of how many runs?).
- Command: `fx-tests test <path> --coverage --limit 0`
- Got: per-config rows only; the summary line gives config counts per platform, not runs/fails.
- Workaround: awk over the coverage table. A per-platform runs/fails line would answer it.
