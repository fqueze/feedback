## Question: in what order did markers within the same millisecond happen, and which were nested inside which?

- Command: `profiler-cli thread markers --search "DOMEvent,Preference,..." --list --limit 0 --session browser-pse-1` over a 3 ms zoom.
- Expected: enough time resolution to tell that `Preference Read ui.popup.disable_autohide`, `popuphiding` and `popuphidden` happened *inside* the 498 us `resize - window` DOMEvent (i.e. were caused by its listeners).
- Got: every row printed `t=39.507s` / `t=39.506s`; start times rounded to 1 ms, and no end time, so nesting was invisible.
- Workaround: `profiler-cli marker info m-721 m-158 ... --json` piped through a python script to print start/end in ms with microseconds.
- What would have answered it: `--list` printing start times at the zoom's resolution (e.g. `39506.604` when the zoom is a few ms), or an indentation/"inside m-158" hint for markers nested within an interval marker on the same thread.

## Minor: `--search resize` is swamped by RefreshDriverTick tick-reason text

- Command: `profiler-cli thread markers --search "resize" --list --limit 0`
- Got: 2,373 markers, almost all `RefreshDriverTick` whose Details contain "resize observers"; the DOM `resize` events were lost among them.
- Workaround: `--search "eventType:resize"`. Worth a hint in `thread markers --help` that DOMEvent types are best searched as `eventType:<type>`.

## Question (review): which markers belong to one document (innerWindowID 753)?

- Command: `profiler-cli thread markers --session browser-review-browser_policy_search_engine.js-1 --search innerWindowID:753 --list --limit 60`
- Expected: the markers whose payload has `innerWindowID: 753`. `marker info` prints that value under "Other payload fields (no schema)".
- Got: `0 markers`. `innerWindowID` cannot be searched, and `--list` does not print it.
- Workaround: `--search DOMEvent --list --limit 0 --json`, then a python filter on `flatMarkers[].data.innerWindowID`. Did the same for `RefreshDriverTick`, to tell the tab's ticks from the chrome ones.
- What would have answered it: allow `innerWindowID:` in `--search`, or print the window ID (or its URL) in `--list` rows.

## Minor (review): loading a raw Taskcluster profile URL selects a content thread

- Command: `profiler-cli load https://firefox-ci-tc.services.mozilla.com/.../profile_browser_policy_search_engine.js.json --session browser-review-browser_policy_search_engine.js-3`
- Got: `t-42 (GeckoMain, Privileged Content)` selected, not the parent `GeckoMain` (`t-0`). A `thread markers --category Test` then showed 0 markers with no hint why.
- Workaround: `thread select t-0`.

## Minor (review): `--session` before the subcommand is rejected

- Command: `profiler-cli --session S thread markers ...`
- Got: `error: unknown option '--session' (Did you mean --version?)`. It works only after the subcommand.
