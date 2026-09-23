## A zoom range outside the profile is accepted silently

- Command: `profiler-cli zoom push 877.9,949 --session <s>` on a 949 ms profile (I meant milliseconds).
- Expected: an error or warning that the range starts after the profile ends, or a hint that the unit is seconds.
- Got: `View: ts>10T→ts>10y (1m11s)` and then `0 markers in view` for every query, which reads like "no such markers".
- Workaround: `zoom clear`, then `zoom push 0.8779,0.949`.

## Which marker a link's `marker=N` points to (review-test_dictionary_replacement.js)

- Question: does the report's link `...marker-table/?marker=56823...` open the marker it quotes (`DictionaryOriginReader::Start` at 256.94 ms)?
- Command: `profiler-cli marker info m-25 --session <s>`.
- Expected: the marker index, the number a profiler.firefox.com link carries, next to the handle (for example `Marker m-25 (index 56823)`).
- Got: name, type, time, fields; no index. Only `--json` has `markerIndex`, and with several handles it is nested under `markers`, while one handle gives the marker object itself, so the same script cannot read both shapes.
- Workaround: one `marker info <m> --json` per marker, piped through python for `markerIndex`. Each of the 31 marker links in this report needed that.
