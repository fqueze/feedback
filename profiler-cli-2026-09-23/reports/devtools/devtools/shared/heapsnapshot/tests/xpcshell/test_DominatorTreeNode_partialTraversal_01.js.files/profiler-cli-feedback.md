## `marker info --json` changes top-level shape with several handles (review)

- Command: `profiler-cli marker info m-17 m-18 --json --session …`
- Expected: the same record shape as `marker info m-17 --json`, in a list.
- Got: a `{"type": "marker-info-multi", "markers": [...]}` wrapper. A script written against the single-handle shape silently printed `None None`.
- Workaround: read `d["markers"]`.
- Question behind it: "the markerIndex and start of these N markers", to check a report's links. A `--fields markerIndex,start` text mode would answer it without a script.
