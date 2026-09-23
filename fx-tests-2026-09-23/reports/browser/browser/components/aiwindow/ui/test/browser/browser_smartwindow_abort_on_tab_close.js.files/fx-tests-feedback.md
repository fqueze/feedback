## `--bugs` with no matching bug prints nothing about bugs

- Command: `fx-tests test browser/components/aiwindow/ui/test/browser/browser_smartwindow_abort_on_tab_close.js --bugs`
- Expected: a "Bugs: none" (or similar) line, so "no bug" is distinguishable from "the flag was ignored / the query failed".
- Got: exactly the default output, with no bug section at all.
- Workaround: searched Bugzilla's REST API by hand (`/rest/bug?quicksearch=...`).

## Question: the passing runs of this test on the same config, to compare with the failing ones

- Goal: check that in passing jobs the earlier test `browser_aiwindow_urlbar_chat.js` ran further than ~120 s before this one (the failing jobs all have it 118–130 s before).
- Commands tried: `fx-tests test <path> --task-ids`, `--profiles`, `--coverage`: all list failing tasks only.
- What would have answered it: a `--task-ids --passing` (or `--status pass`) listing a few passing task IDs per config, so their logs can be compared.
- Workaround: none; the comparison was left out.

## Question: which console error preceded the timeout, in each failing run

- The failure message is always `Test timed out`; the cause is a `console.error: "Failed to create OpenAI engine:" (new Error("Engine was terminated before initialization completed." ...))` logged between TEST-START and the timeout.
- Commands tried: `fx-tests test <path> --task-ids --limit 0`, `fx-tests task <id> --messages`: they show the TEST-* messages only.
- Workaround: downloaded 17 `live_backing.log` files and grepped each between this test's TEST-START and its timeout line (a shell loop): 17/17 had that same error.
- What would have answered it: an option on `task` (or `test --task-ids`) printing the `console.error` / `JavaScript error` lines logged during the failing test, grouped by message across tasks.
