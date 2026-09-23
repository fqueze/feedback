## `marker info` shows string-table indices for Log marker fields
- Command: `profiler-cli marker info m-305 --session <s>` on a DocShellAndDOMWindowLeak Log marker (per-test profile of task O5oFDzaSTJSeINejQth4yA).
- Expected: `Message: ++DOMWINDOW == 11 (162e2b400) [pid = 3533] [serial = 158] [outer = 163989f00]`, `Level: Info`.
- Got: `Message: 52894`, `Level: 5460` (the `formattedValue`; `--json` has the right text in `value`).
- Workaround: `--json`.

## `thread markers --search` does not match Log marker message text
- Command: `profiler-cli thread markers --search "serial = 172" --list --session <s>`
- Expected: the `++DOMWINDOW ... [serial = 172]` Log marker.
- Got: "No markers match", although the marker exists (found by scripting over `--search name:DocShellAndDOMWindowLeak --list --json`). Probably the same string-index problem as above.
- Question it cost: "when was the window with serial N created, and in which outer window?"

## `profile logs` ignores the zoom range
- Command: `profiler-cli zoom push m-6` then `profiler-cli profile logs --module DocShellAndDOMWindowLeak --thread t-0 --limit 10`
- Expected: the log entries inside the 1.469 s zoom.
- Got: header shows the zoomed view, but entries start at the beginning of the profile (446 of 446, the full-profile count).

## (review) Which marker does this link point at?
- Command: `profiler-cli load "<profiler.firefox.com URL with marker=17636>" --session <s>`, then `profiler-cli status --session <s>`.
- Expected: the load, or `status`, names the marker the URL selects (its handle, name, time), so a reviewer can check a link directly.
- Got: nothing about the marker. I had to find each quoted marker with `thread markers --search`, then run `marker info --json` to compare `markerIndex`, for 11 links. `marker info` accepts only handles, not an index.

## (review) `--search` does not match DOMWindow marker labels
- Command: `profiler-cli thread markers --search "serial = 172" --list` on a resource-usage profile (task MMZ8eXUJT7WG7OgC8egaxw).
- Expected: the `DOMWindow` marker whose label shows `serial = 172`.
- Got: only the TEST-INFO line. The label is built from a numeric field, so only `--search serial:172` finds it. Searching a text you can see in a row's label should find that row.
