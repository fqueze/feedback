## Which document a refresh driver is ticking for

- Question: which page a series of `RefreshDriverTick waiting for paint` markers belongs to.
- Command: `profiler-cli marker info m-575 --session <s>` (and `thread markers --group-by field:innerWindowID`)
- Expected: the innerWindowID resolved to the page's URL from the profile's pages table (e.g. `moz-extension://…/popup.html`), as the profiler UI does.
- Got: `innerWindowID: 4294967338` only, and `--search 4294967338` matches nothing (numeric raw field).
- Workaround: `marker info <DocumentLoad handle> --json` and compare its `rawFields` innerWindowID by hand. The default output could show the URL next to any innerWindowID.
