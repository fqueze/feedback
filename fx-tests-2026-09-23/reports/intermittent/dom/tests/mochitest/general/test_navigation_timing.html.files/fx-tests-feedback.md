## Question: do all failing runs of a test fail with the same set of messages?

- Command: `fx-tests test dom/tests/mochitest/general/test_navigation_timing.html --task-ids --limit 0`, then `fx-tests failures --harness mochitest --message unloadEvent --tests`
- Expected: per failing run, all its failure messages (here: whether `unloadEventEnd should be 0` always accompanies `unloadEventStart should be 0`, or ever fails alone — this decides whether the two values share one random draw).
- Got: only the first failure message per run ("Issues (first failure per run)"); `failures` also only groups by first message. The second message is visible only one task at a time with `fx-tests task <id> --messages`.
- Workaround: ran `fx-tests task --messages` on a few tasks; relied on "no run has `unloadEventEnd` as its first message" as indirect evidence.
- What could have shown it: a `--messages` option on `fx-tests test` listing the distinct message sets across failing runs, with counts.
