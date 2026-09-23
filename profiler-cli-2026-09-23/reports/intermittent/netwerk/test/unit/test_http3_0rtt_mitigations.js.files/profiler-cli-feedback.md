
## Question: the network loads (`Load N: <url>`) of the test, and nothing else (review-test_http3_0rtt_mitigations.js)

- Command: `profiler-cli thread markers --session <id> --search "Load " --list --limit 0`
- Expected: the `Load N: ...` network markers; the trailing space was meant to exclude words merely containing "load".
- Got: the space is trimmed, so the term matched every marker with "load" anywhere in its name or payload (`Preference Read` of `browser.cache.disk.preload_chunk_count`, `ScriptLoadRequestList::AppendElement`, `load_file`, `DOMEvent load`...), ~200 lines to find 12 loads.
- Workaround: `--category Network --search "Load 1"`. A `markerType:Network` hint in the search-syntax help, or keeping quoted whitespace, would have answered it directly.
