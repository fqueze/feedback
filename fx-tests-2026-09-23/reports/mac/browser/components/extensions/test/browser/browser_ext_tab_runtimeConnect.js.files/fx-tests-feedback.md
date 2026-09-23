# fx-tests feedback (browser_ext_tab_runtimeConnect.js)

## Question: in which of a leaker's failing jobs did a later test also fail, and why not in the others?

- Question: "the leaker failed in 48 jobs and my test in 33: in the other 15, did my test run
  after it in the same browser, and what ended the chain before it?"
- Command: `fx-tests test <leaker> --task-ids --limit 0` and `fx-tests test <victim> --task-ids
  --limit 0`, diffed by hand, then `fx-tests task <id> --passed --limit 0` for each of the 11
  jobs left, grepping for the victim's row and for a TIMEOUT or CRASH between the two.
- What the output could have shown: a co-failure view, e.g. `fx-tests test <victim> --after
  <leaker>`, listing the jobs where the leaker failed with the victim's status in each, and any
  TIMEOUT/CRASH (browser restart) between the two in manifest order.

## The two tests' windows differ, so their job lists do not line up

- `fx-tests test` for the leaker (read earlier today) covered 2026-08-31 … 2026-09-20; for this
  test, 2026-09-01 … 2026-09-21. One job where both failed (ceSBpKIuRIOtH6NJf9bHSQ, 2026-08-31)
  only appears in the leaker's list, which looked like a leaker failure without a victim
  failure until checked with `fx-tests task`.
- Expected: a way to pin the window (`--since`/`--until`) that is printed next to each list so
  two lists can be compared, or a warning when a date falls outside the current window.
