## Question: in what order, to the millisecond, did these markers happen, and in which window?

- Command: `profiler-cli thread markers --search "RefreshDriverTick,Reflow,ViewManagerFlush,Test,DOMEvent" --list --limit 0 --session S` (after `zoom push 79.9,80.4`)
- Expected: a start time precise enough to order markers inside a 500 ms zoom, and the marker's innerWindowID (which browser window it belongs to).
- Got: every row printed `t=1m20s`, so ~250 rows inside one second were indistinguishable in time; no innerWindowID column. The `--json` flat list has `start` but its `data` omits `innerWindowID` for most marker types (it is only under `rawFields` in `marker info --json`), so telling the new window's reflows from the main window's took one `marker info --json` call per handle.
- Workaround: `--json` plus a python one-liner to print `start/1000` with 3 decimals, and a loop of `marker info <h> --json` to read `rawFields.innerWindowID`.
- What would have answered it: print `t=` with ms precision when the view is under a few seconds (or always, e.g. `80.339s`), and an optional `--group-by`/column for innerWindowID in `--list`.

## Question (review): which marker does a link's `marker=N` point at?

- Command: `profiler-cli load "<profiler.firefox.com link with marker=47861>" --session S`, then `thread markers --search SetNeedStyleFlush --list` and `marker info <h> --json` on candidates.
- Expected: `load` (or `status`) to say which handle the link's `marker=N` resolves to, or a `marker info --index 47861`.
- Got: the link's marker is not surfaced; the report's text ("commit `SetURI`") suggested a SetNeedStyleFlush, but index 47861 was a `DOMEvent SetURI`. Finding it took dumping every handle in a 10 ms range and one `marker info` call over all of them, filtering on `markerIndex`.
- Workaround: `thread markers --list --json` over a narrow zoom, then `marker info <all handles> --json | python` to match `markerIndex`.
- What would have answered it: `load <link>` printing "marker=N is m-1900 (DOMEvent SetURI, 3.0035s)", or a lookup by index.
