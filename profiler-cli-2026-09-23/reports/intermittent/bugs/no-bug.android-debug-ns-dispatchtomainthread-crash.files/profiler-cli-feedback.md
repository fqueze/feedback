## review-no-bug.android-debug-ns-dispatchtomainthread-crash: which marker does this link point to?

- Command: `profiler-cli load "<profiler.firefox.com URL with marker=30743&thread=0>" --session ...`, then `profiler-cli marker info m-30743`.
- Expected: `load` of a link carrying `marker=N` to say which handle that is (e.g. "Selected marker: m-16 CRASH"), or `marker info` to accept the index from the URL.
- Got: `Error: Unknown marker m-30743`. I had to search for the marker by text, then check `markerIndex` in `marker info --json`, once per link (7 links).
- Along the way, `thread markers --search CRASH --list` matched 2082 markers, because the bare term also matches payload values such as `libcrashhelper.so` and `crashreporter`. The output was 94 KB. `name:CRASH` or `--search NS_DispatchToMainThread` would have been the right query.
- Workaround: search by the quoted text, then `marker info <m-…> --json | grep markerIndex`.
