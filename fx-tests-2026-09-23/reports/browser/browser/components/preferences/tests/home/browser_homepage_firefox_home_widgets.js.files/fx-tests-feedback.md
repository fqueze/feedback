## When did this test's duration step up on one config?

- Question: the failure is a 45 s duration check; did the passing duration on the a11y-checks configs move, and when?
- Command: `fx-tests test <path> --durations` gives one distribution over the whole 21-day window; `--history` has pass/fail counts per day but no durations.
- Workaround: a shell loop over `fx-tests test <path> --durations --day <d> --config a11y` for 7 days, which showed the median moving from ~39.5 s (09-02..09-14) to ~42.5-43 s (09-16..09-21).
- What would have answered it: `--history --durations` printing the per-day median (and p95) next to the pass/fail counts, per config.

## `--bugs` says nothing when no bug is found

- Command: `fx-tests test browser/components/preferences/tests/home/browser_homepage_firefox_home_widgets.js --bugs`
- Expected: a "Bugs: none naming this test" line. Got: the ordinary output with no bug section at all, so I could not tell "no bug" from "flag ignored"; checked Bugzilla by hand.
