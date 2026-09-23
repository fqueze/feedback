## Substring search on marker names cannot be anchored

- Command: `profiler-cli thread markers --session <s> --search "Dictionary,Load ,Starting" --list --limit 0`
- Expected: the network `Load N: <url>` markers.
- Got: every `DllLoad` and `~ScriptLoadRequest` marker too, hundreds of lines on a Windows debug profile, which I then had to filter with grep.
- Workaround: `grep -v DllLoad` on the output. A prefix or exact-name form (e.g. `name:^Load `) would avoid it.

## Review: marker times too rounded to tell whether one marker nests in another

- Question: did `DictionaryOriginReader::OnCacheEntryAvailable` run synchronously inside `DictionaryOriginReader::Start` (i.e. does its start fall inside Start's interval)?
- Command: `profiler-cli marker info m-137 m-138 --session <s>`
- Expected: start and end precise enough to compare (µs), or a "nested inside m-137" hint.
- Got: `Time: 1.165s - 1.165s (13.100μs)` for both; the list view is rounded to 10 µs too.
- Workaround: `marker info --json` and a python one-liner printing `start`/`end` to 4 decimals. Same one-liner was needed to get `markerIndex` for checking 50 report links, which the default output does not show either.
