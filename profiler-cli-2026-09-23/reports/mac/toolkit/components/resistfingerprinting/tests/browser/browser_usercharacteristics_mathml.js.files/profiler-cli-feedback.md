## Question: which tests ran in this per-test profile's browser session, one row per test file, with status

- Command: `profiler-cli thread markers --category Test --list --limit 0 --search "name:test"`
- Expected: only the `test` interval markers (`PASS — <path>` / `FAIL — <path>`), one per test file.
- Got: 1,890 rows; `name:` is a substring match, so `TestUtils`, `TEST-PASS`, `task`... all matched. `--search "PASS —,FAIL —"` matched nothing (the label text is not searchable that way).
- Workaround: `--search "toolkit/.../browser_" --json` and a Python filter on `name == "test"`.
- What would have answered it: an exact-name match (`name=test`, or `--name test`).

## Question: in which order did three markers fire within the same second

- Command: `profiler-cli thread markers --search "Text::set,NotifyObservers,TEST-UNEXPECTED-FAIL,Ping::submit" --list`
- Expected: timestamps precise enough to order a metric set, an observer notification, a failure and a ping submit that are 0.5 ms apart.
- Got: every row shows `t=2m58s`; the order within the second is only the row order, and the gap is not shown.
- Workaround: `--json` and print `start` with 4 decimals.
- What would have answered it: millisecond timestamps in `--list` output (at least when the view spans less than a few minutes, or with a `--precise` flag).

## Session directory not writable in a sandboxed agent

- Command: `profiler-cli load <url> --session <id>`
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`; the message did suggest `PROFILER_CLI_SESSION_DIR`, which worked.
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` on every call (wrapper script).

## Question (review): where does this session set `characteristics.canvasdata1`, the text a `String::set` row shows

- Command: `profiler-cli thread markers --search 'characteristics.canvasdata1' --list --session …`
- Expected: the `String::set characteristics.canvasdata1 : 7c3f…` rows the same list prints for a broader search.
- Got: `0 markers`. The row label is built from two payload fields (`cat` = `characteristics`, `id` = `canvasdata1`), and `--search` matches each field separately, so the text as displayed never matches. At first this looked like "the metric was never set", the opposite of the answer.
- Workaround: `--search 'canvasdata1 '` and a Python filter on `label`.
- What would have answered it: `--search` also matching the formatted label, or a note in the output when a term with a `.` matches no single field.
