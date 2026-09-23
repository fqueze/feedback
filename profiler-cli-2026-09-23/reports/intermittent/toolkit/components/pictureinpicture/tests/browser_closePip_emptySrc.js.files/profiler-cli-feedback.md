## Question: in what order, to the millisecond, did these markers happen?

- Command: `profiler-cli thread markers --category Test --search browser_closePip_emptySrc.js --list --limit 0 --session <s>` (and the same with `--search PictureInPicture`, `--search DOMEvent`), on a 1m10s profile; also `profiler-cli marker info m-37 m-38 ...`.
- Expected: start times precise enough to order a 30 ms race across two processes (spawn reply, `emptied`, `setTimeout`, `SendQuery`, `waitForCondition` giving up, `TEST-UNEXPECTED-FAIL`).
- Got: every row printed `t=1m5s` / `t=1m6s`, and `marker info` printed `Time: 1m5s - 1m5s (8.406ms)`: one-second resolution, so a dozen markers within 50 ms all showed the same time and the list order was the only clue (and `--list` put m-10 `Testing no-controls case.` after m-16 while it came 48 ms later — fine — but gave no way to tell 2 ms from 900 ms apart).
- Workaround: `--json` piped into a 5-line script printing `start` and `duration` with 0.1 ms precision, for every marker query (about 10 times in this investigation).
- What the output could have shown: when the view (or the zoom) is short, or when rows share the same second, print start times with ms (e.g. `t=65.3553s`), and in `marker info`, the start and end in ms.
