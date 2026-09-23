## Question: which marker does this link's `marker=N` select? (review)

- Command: `profiler-cli load '<profiler.firefox.com link with marker=871120>' --session …`, then `profiler-cli marker info m-58 m-67 … --session …`.
- Expected: `load` of a link names the marker the link selects (handle + name + time), or the text output of `marker info` shows the marker index, so that 15 links can be checked against the markers they quote.
- Got: `load` prints only thread/range state. Text `marker info` has no index. I had to run `marker info <m> --json` once per marker and pipe it through a python one-liner to print `markerIndex`.
- Workaround: `--json` plus a script, one call per marker.
