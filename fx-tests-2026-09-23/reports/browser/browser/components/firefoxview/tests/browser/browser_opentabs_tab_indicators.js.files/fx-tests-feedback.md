## Question: which failing runs of this test have a per-test profile?

- Commands: `fx-tests test <path> --profiles` (lists only resource-usage profiles, 421 of them), then the suggested `fx-tests intermittent --test <path> --profiles` (listed a single per-test profile, from the one annotated test-verify job).
- Expected: the per-test profile URLs of the failing runs, or a count of runs that have one.
- Got: nothing pointed at the ASan runs, which do upload per-test profiles; the TSan runs (the 95% majority) upload none. I found the ASan one only by running `fx-tests task <id> --profiles` on tasks one by one.
- Could show: per config, how many failing runs named a per-test profile, and the URLs (e.g. `fx-tests test <path> --profiles --per-test`), plus a note when a config (TSan here) never uploads one.
