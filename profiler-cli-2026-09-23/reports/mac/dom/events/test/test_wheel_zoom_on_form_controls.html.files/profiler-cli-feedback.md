## Question: the millisecond timestamps of a sequence of markers (event ordering within one second)

- Command: `profiler-cli thread markers --search DOMEvent --list --limit 0 --session <s>` (zoomed to a 6 s range around the failure)
- Expected: start times precise enough to order a wheel event, a `DoZoomReduce` and a `TEST-PASS` that are 1-5 ms apart.
- Got: every row printed `t=3m35s`; the list is chronological but the times are rounded to the second, so gaps like "the timeout ran 11 ms after the second tap" cannot be read.
- Workaround: `--json` and a script printing `start/1000` with 3 decimals. The same for `marker info m-a m-b ...`, whose text output also rounds (`Time: 3m35s`).
- What could show it: milliseconds (`t=215.320s`) in `--list` rows once the view is shorter than a minute, or a `--precise-times` flag.

## Question: the per-test `test` markers of one manifest (which tests ran, when, in this browser)

- Command: `profiler-cli thread markers --category Test --search 'name:test' --list --limit 0 --session <s>`
- Expected: the `test` interval markers only (one per test file).
- Got: 11,452 markers: `name:test` also matches every `TEST-PASS` whose payload has a `test` field, as the guide warns for Text markers.
- Workaround: `--json` and a script keeping `m["name"] == "test"`.
- What could show it: an exact-name filter (`--name test`) separate from payload search.

## Question: which thread and which window a screenshot shows, and the window size

- Command: `profiler-cli screenshots --at <t> -o <dir> --session <s>`
- Expected: the window size next to each written image, since it is in the marker payload.
- Got: only file names; the window size needed `marker info m-N` per screenshot, and the frame's time span also needed `marker info --json` (text rounds to the second).
- Workaround: `marker info` on the screenshot handle from the file name.
