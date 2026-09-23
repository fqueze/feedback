## Question: "at what time were the samples containing function X taken?"

- Context: a sparse Windows ASan profile, 23 samples in 450 ms. I needed to know whether the one `_updateGridPanel` sample came before or after the click.
- Command: I had to loop `profiler-cli zoom push a,a+0.02` / `thread samples-top-down --search _updateGridPanel --json` / `zoom pop` over 20 ms windows and read `regularCallTree.totalSamples` each time.
- What would have answered it: sample timestamps for a `--search` (for example `thread samples --search X --list` printing one line per matching sample with its time and leaf frame), or a time histogram on `samples-top-down --search`.

## Question: "which marker index is handle m-N?" (review of this report)

- Context: checking a report's links means checking that each `marker=N` is the marker the text quotes. The review brief says to compare `markerIndex` from `marker info --json`.
- Command: `profiler-cli marker info m-119 --session …` prints type, time, fields, but no index; I looped `marker info <m> --json | python3 -c '…d["markerIndex"]'` over 19 handles.
- What would have answered it: the marker index in the default `marker info` output (or a `--search index:300094` / `marker find --index N` lookup from a link's `marker=` value to a handle).

## `profile-link.py` refuses a session loaded from a profiler.firefox.com link (review of this report)

- Command: `profiler-cli load 'https://profiler.firefox.com/from-url/…/marker-table/?marker=300574&thread=0&v=17' --session X`, then `profile-link.py --session X --thread t-0 --range 44.650,44.925 --panel stack-chart`.
- Expected: a link, since the review brief says to open the report's links with `profiler-cli load <link>`, and the from-url link carries the Taskcluster URL.
- Got: `A link needs the raw artifact URL: load the Taskcluster URL itself, not a local copy or a profiler link.`
- Workaround: loaded the same profile a second time from the raw Taskcluster URL (another 0.6 GB session) just to build one link. `profile-link.py` could decode the `from-url` component itself.
