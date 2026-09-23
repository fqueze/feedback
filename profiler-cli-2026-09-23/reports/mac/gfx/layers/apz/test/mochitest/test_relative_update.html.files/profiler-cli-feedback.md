## Marker list times are rounded to the second in a long profile, even when zoomed to 170 ms

- Command: `profiler-cli thread markers --search DOMEvent,NotifyObservers --list --limit 0 --session <s>` after `zoom push 382.28,382.45`, in a 6m22s profile.
- Expected: start times precise enough to order the markers in the zoomed range (ms, e.g. `t=382.385s`, or relative to the zoom start).
- Got: every row printed as `t=6m22s`, so the ordering and gaps between the test's steps (wheel event, paints, repaint flush) cannot be read from the list.
- Workaround: `--json` and a script printing `start` with 3 decimals.
- Question the default output did not answer: "how many ms after the wheel event did the first paint come?"

## IPC markers in `--list` show only "IPCIn"/"IPCOut", not the message type

- Command: `profiler-cli thread markers --search IPC --list --limit 0 --session <s>` (zoomed to 170 ms around a test).
- Expected: the message type (e.g. `PAPZ::Msg_RequestContentRepaint`) and the peer in each row, as the profiler UI's marker table shows.
- Got: 319 rows reading `IPCIn` / `IPCOut` with a duration and nothing else.
- Workaround: `--json`, reading `data.messageType` and `data.niceDirection`.
- Question the default output did not answer: "did APZ send a repaint request to the content process after the wheel event, and when?"

## Finding a passing run of a test needs a detour through another test's profile

- Question: "what does a passing run of this test look like, in a per-test profile?" (the retry passed, no profile).
- What worked: `fx-tests test` on each later test of the same manifest to find one that failed on a mac job, then `profiler-cli profile markers --search test_relative_update` on that test's profile, which held a PASS of this test (`IbKLawMaRn-PwnIsE5FbxA`, `profile_test_wheel_scroll.html.json`).
- What could have shown it: `fx-tests test <path> --profiles` listing, for a test, other tests' failure profiles from the same browser session that ran after it and so contain a passing run.
