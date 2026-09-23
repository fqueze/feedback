# fx-tests feedback

## Question: "does a bug name this test?"

- Command: `fx-tests test devtools/client/debugger/test/mochitest/browser_dbg-windowless-workers-early-breakpoint.js --bugs`
- Expected: a Bugs section, or a line saying none was found.
- Got: exactly the default output, nothing on stdout or stderr about bugs, so "no bug" and "the lookup did not run" look the same.
- Workaround: queried Bugzilla REST by summary substring by hand (found only closed bugs 1543596, 1559761, 1565462, 1864471).
- Could have shown: `Bugs: none annotated in the window` (and maybe the closed ones naming the test).

## Question: "at which step of the test did each of these timeouts happen?"

- Command: `fx-tests test <path> --task-ids --limit 0`, `fx-tests task <id>`
- Expected: some way to see the last test-log line before `Test timed out`, since every timeout shares one message.
- Got: only `TIMEOUT Test exceeded time limit` for all 16, so two failure modes (12 at `startWorkerWithMessage()`, 4 at `Waiting on sources: simple-worker.js`) were one row.
- Workaround: downloaded the 16 `live_backing.log` files and awk'd the last `Invoking in tab`/`Waiting on sources` line before the timeout.
- Could have shown: for timeouts, the last INFO line before the timeout, grouped like the Issues block.
