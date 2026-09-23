## `thread markers --list` prints start times rounded to the second

- Command: `profiler-cli thread markers --session <s> --category Test --search browser_tab_groups_list --list --limit 0`
- Question: in what order, to the millisecond, did the test's steps happen (the click, the assertion, the navigation commit)?
- Expected: start times with ms precision (the profile is 7 minutes long, and a whole test task runs within one second).
- Got: `t=7m4s` for every marker of four subtests, so ordering inside the failing subtest was impossible.
- Workaround: `--json` and printing `start/1000` with a script. A `--precision` flag, or ms precision whenever the listed markers span less than a few seconds, would answer it.
## IPC marker payload times disagree with the marker's own times (review)

- Command: `profiler-cli marker info m-269 --session <s>` (retry profile fo_DZyo2Tqe82m1FM-VXBQ `-2`, `PBrowser::Msg_OnLocationChange`)
- Question: when did the content process send this message, relative to the test's assertion?
- Expected: one send time.
- Got: the text output gives `Time: 7m4s - 7m4s` (to the second) and then, under "Other payload fields (no schema)", `startTime`/`sendStartTime`/`recvEndTime` of 4676.1 ms. The `--json` `start` is 4665.9 ms. The raw payload fields are offset by 10.2 ms in this profile (0.3 ms and 7 ms in two others), and causality shows the payload is the wrong one: it has the redirect sent after the content already built the document. The report I was reviewing had used the payload value and got the order against the assertion backwards.
- Workaround: `--json` `start`/`end`, cross-checked against the Runnable that handles the message.
- Would help: the marker's time in ms in the text output, and the payload times either shifted to the same time base or left out.
