## Question: which tests ran in this browser session before mine, in order, with their status

- Command: `profiler-cli thread markers --session <s> --category Test --search name:test --list --limit 0`
- Expected: only the per-test `test` interval markers (`PASS — <path>`).
- Got: 470 markers, because `name:test` is a substring match and also hits `TEST-PASS`, `TestUtils`, `task`, ...; had to `grep -E '^\s+m-[0-9]+\s+test\s'` on the output.
- Workaround: grep. An exact-name match (e.g. `name:=test`) or a `--name` filter would answer it directly.

## Question: does this link's `marker=N` point at the marker the report quotes (review)

- Command: `profiler-cli marker info m-15586 --session <s>`
- Expected: the marker's index in the thread's marker table, the N that profiler.firefox.com links use.
- Got: type, category, time, fields; no index. Had to run `marker info m-… --json` through a python one-liner to read `markerIndex`, for every link checked (13 links).
- Workaround: `--json` + script. Printing `Index: N` in the default output would answer it.

## Question: find the DOMEvent markers the list shows as `resize - window` / `keyup - input` (review)

- Command: `profiler-cli thread markers --session <s> --search 'resize - window' --list --limit 0` (same with `'keyup - input'`)
- Expected: the markers the list prints with exactly that description.
- Got: `No markers match`, since the printed description is assembled from the event type and target fields, and the search matches each field separately.
- Workaround: `--search name:DOMEvent --list --limit 0` and grep the output. Matching the printed description too would let a phrase copied from the output find itself.
