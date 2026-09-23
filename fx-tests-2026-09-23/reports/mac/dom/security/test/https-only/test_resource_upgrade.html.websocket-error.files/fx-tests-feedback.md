## Question: how many failures of this one failure mode, per config?

- Command: `fx-tests test <path> --task-ids --issue 4 --limit 0`
- Expected: the per-config table restricted to issue 4 (`Unexpected response from server (websocket-unexpected-error)`), with rates.
- Got: the per-config table for all failure modes together (timeouts included), then a flat list of task IDs per day. The per-config counts for this mode had to be rebuilt with a regex over the task list, and the rates by dividing by the all-modes run counts.
- Could have shown: `--issue <n>` also filtering the "Failing configurations" table.

## Question: has this failure mode stopped?

- Command: `fx-tests test <path> --history --config macosx1500`
- Expected: per-day counts for one failure mode (`--issue 4`).
- Got: pass/fail/timeout per day. It only worked because this mode happens to be the only `FAIL` one, and the other mode is the only `TIMEOUT`. With two `FAIL` modes the question could not have been answered.
- Could have shown: `--history --issue <n>`.

## Question: since when does this failure mode exist, and on which trees?

- Command: `fx-tests intermittent --bug 1840915`
- Expected: the bug's annotations over a longer period, split by failure message and tree, to see whether the WebSocket mode predates a suspect landing (bug 2066413, 2026-08-26), and whether it also fails on ESR.
- Got: 7 days of annotations. `fx-tests test` covers 21 days of trunk only.
- Workaround: `curl https://treeherder.mozilla.org/api/failuresbybug/?startday=2026-05-01&endday=2026-09-22&tree=all&bug=1840915`, then a Python script over its `lines`. That showed the mode on mozilla-esr140 (macOS 14.7), beta and release since May, which ruled out every recent Necko and ssltunnel change. Three calls.
- Could have shown: `fx-tests intermittent --bug <N> --since <date>`, with a per-message, per-tree, per-month breakdown.
