## Question: in what order did these markers happen, to the millisecond

- Command: `profiler-cli thread markers --category Test --list --limit 0 --session <s>` (also `--search DOMEvent --list`)
- Expected: times precise enough to order events inside one second.
- Got: `t=8m19s` for about 100 consecutive markers. Here what mattered was 0.4 ms: an `EnsureFocus` reply at 499.1036 s against `smartbar-initialized` at 499.1040 s.
- Workaround: `--json` and a Python one-liner printing `start/1000` with 4 decimals, merged across several `--search` calls with `sort -n`.
- What would have answered it: a `--precise` flag, or ms precision automatically when the view is under a minute or neighbouring rows share the same second.

## `profile markers --search` does not match a DOMEvent's displayed label

- Command: `profiler-cli profile markers --search 'keydown - input' --session <s>`
- Expected: the `DOMEvent keydown - input@10d124040 id="email"` markers that `thread markers --search DOMEvent` lists.
- Got: "No markers match the specified filters (searched 157 threads)." `--search keydown` does match, but mostly on `Preference Read ... hide_on_keydown` markers.
- Workaround: find the thread by another marker (`SmartFormFill:IsSmartWindow`), then `thread markers --search name:DOMEvent` on it.

## Question: which thread handle does a link's `thread=` value name (review-browser_smartwindow_smartformfill_autocomplete.js)

- Command: `profiler-cli load '<profiler.firefox.com link with thread=0>' --session review-sfa-1`, then checking other links to the same profile with `thread=yc`, `thread=y3`, `thread=xs` and `thread=f`
- Expected: a way to go from the URL's `thread=` value to a `t-N` handle, for example `thread select --url yc`, or the URL form in a `thread list` column.
- Got: `load` selects only the thread of the link it loaded. For the other links I had to decode the profiler's URL encoding by hand (`yc` = 76 → `t-76`). I then checked each result by its pid in `thread list`. `profile-link.py` cannot help here: it refuses a session loaded from a profiler link ("A link needs the raw artifact URL").
- Workaround: decoded by hand and cross-checked the pid.

## Question: which marker is `marker=N` in a link

- Command: `profiler-cli thread markers --search ... --list --json --session <s>`, then one `marker info m-K --json` per candidate to read `markerIndex`
- Expected: the marker index in `--list` output (or in its JSON `flatMarkers[]`), or a `marker info --index N` lookup.
- Got: `flatMarkers[]` has no `markerIndex`. Finding the marker behind each of 13 links took a shell loop of `marker info` calls over the candidates.
- Workaround: a loop over `marker info m-K --json`, printing `markerIndex`.
