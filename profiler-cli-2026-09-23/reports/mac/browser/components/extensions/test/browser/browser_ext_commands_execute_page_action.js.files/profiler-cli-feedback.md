
## Which document do these markers belong to? (review-browser_ext_commands_execute_page_action.js)

- Question: which document the 521 `RefreshDriverTick waiting for paint` markers on the
  WebExtensions main thread belong to. The marker has no payload besides `innerWindowID`.
- `profiler-cli thread markers --session S --search "waiting for paint"`: the aggregate shows
  only the name and frequency. `--list` shows no window either.
- `profiler-cli thread markers --session S --search innerWindowID:4294967338` returned 0 markers,
  though `marker info --json` shows that key under `rawFields`.
- `profiler-cli marker info m-160..m-680 --json` failed with "covers 521 handles, more than the
  maximum of 256".
- Expected: grouping or filtering markers by innerWindowID, ideally with that window's URL
  (the `DocumentLoad` and `nsRefreshDriver initial timer start` markers with the same ID carry it).
- Workaround: three chunked `marker info --json` calls and a Python counter.
