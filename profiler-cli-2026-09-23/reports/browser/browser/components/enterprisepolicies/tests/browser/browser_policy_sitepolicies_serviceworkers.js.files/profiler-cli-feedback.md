## Question: "which tests ran in this browser session, in order, with their status?"

- Command tried: `profiler-cli thread markers --session <s> --category Test --search name:test --list --limit 0`
- Expected: one row per `test` marker (`PASS — <path>` / `FAIL — <path>`).
- Got: 2285 rows — `name:` also matches the payload `name` key of every Text marker (BrowserTestUtils, task, TestUtils...), as the guide warns. `--search TEST-START` matched nothing (there are no TEST-START markers in per-test profiles).
- Workaround: `--search 'browser/components/<dir>/browser_' --list --limit 0 | rg '^\s+m-\d+\s+test\s'`.
- What would have answered it: a way to match the marker name only (e.g. `marker-name:test` / `--name test`), or a `profile tests` view listing the harness's `test` markers with status and duration.

## Question (review): "which marker handle is the link's `marker=N`?"

- Command: `profiler-cli marker info <m-…> --session <s> --json`, once per candidate, to read `markerIndex`.
- Expected: a way to go from index to handle, e.g. `marker info --index N` or the index shown as a column of `thread markers --list`.
- Got: rows show only `m-…` handles. With two same-text markers at one timestamp (macOS `cHKc5qwz`, m-1405/m-1406 at 1m23s), you have to guess which one it is and check each.
- Workaround: run `marker info --json` on every candidate.
