## A per-config perma-failure hidden by the window average and a step change

- Command: `fx-tests test browser/components/sidebar/tests/browser/browser_opentabs_hover_preview.js` (and `--history`)
- Expected: a verdict saying it was a perma-failure on the a11y-checks configs until 2026-09-04, then stopped.
- Got: `Verdict: intermittent ... worst is ...swr-a11y-checks at 32.4%`. The unfiltered `--history` shows about 100 failures a day against 800 passes. Only `--history --config swr-a11y-checks` showed `0 pass / 111 fail` per day until 09-04.
- What would have helped: a verdict that looks at the per-config history, e.g. "perma on <config> until <date>, 0 failures since". The window's 32% averages two different regimes.
- Minor: `--since 2026-09-03` is rejected (`expects a non-negative integer`), so a date takes `--day`.
