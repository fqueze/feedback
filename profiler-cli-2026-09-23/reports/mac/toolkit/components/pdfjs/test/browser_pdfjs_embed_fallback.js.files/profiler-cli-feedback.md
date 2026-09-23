## Question: which document (outer page or its <embed> subdocument) does each Styles / Reflow / load marker belong to?

- Command: `profiler-cli thread markers --search "Reflow,Styles,RefreshDriverTick,DOMEvent" --list --limit 0 --session <id>` on a content process holding a page and its in-process subdocument.
- Expected: some way to tell the two documents apart in the list — an `innerWindowID` column, or a `--search innerWindowID:<n>` that works, or grouping by it.
- Got: the flat list shows name/time/duration only; `load - document` appears twice 3 ms apart with nothing to say which is which. Had to run `marker info m-A m-B ...` on each candidate and read `innerWindowID` from "Other payload fields (no schema)".
- Workaround: batch `marker info` on the handful of candidate handles.
- What the output could show: the inner window id (or the document URL it maps to, which the profile has in its pages table) next to each marker in `--list`, at least when the thread has more than one page.

## Minor: `--search "name:Reflow,name:Styles,..."` matched far more than those marker names

- Command: `profiler-cli thread markers --search "name:Reflow,name:Styles,name:RefreshDriverTick,..." --list`
- Expected: only markers named Reflow/Styles/RefreshDriverTick.
- Got: 200+ rows including Runnable/IPC/Preference markers (the documented caveat that `name` is also a payload key). Workaround: bare terms plus a `grep -E "  (Reflow|Styles|RefreshDriverTick) "` on the output. A way to match the marker name only (e.g. `marker:Reflow`) would avoid the pipe.
