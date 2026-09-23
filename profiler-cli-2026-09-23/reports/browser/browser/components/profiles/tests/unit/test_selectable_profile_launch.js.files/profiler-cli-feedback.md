## Question: which marker does a link's `marker=N` point to?

- Command: `profiler-cli marker info m-152 --session <s>` (then `--json` piped through python to read `markerIndex`, once per handle, for 21 links).
- The default output of `marker info` does not show the marker's index in the thread, so checking a link's `marker=N` needs `--json` and a script. Printing `Index: 3942` in the default output, or accepting `marker info --index 3942`, would have answered it.
