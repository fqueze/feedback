## Question: "which profiler URL `marker=N` does handle m-X correspond to?" (review of browser_getSecurityInfo.js)

- Command: `profiler-cli marker info m-260 --session <s>` (and with several handles, `marker info m-257 m-259 m-24 ...`)
- Expected: the marker's index in the thread (the value a profiler.firefox.com `marker=` link carries) in the default text output, so each quoted link can be checked against its marker at a glance.
- Got: name, type, category, time, fields, but no index; it is only in `--json` as `markerIndex`.
- Workaround: loop over handles with `--json` and a python one-liner printing `markerIndex`, name and start. A `marker info` line such as `Index: 411533`, or `thread markers --list` printing the index next to the handle, would have answered it with no script.
