# profiler-cli feedback (test_protocol_types.js)

## A bare `-term` exclusion in `thread markers --search` silently matches nothing

- Command: `profiler-cli thread markers --session <s> --search "-name:CPU Use,-name:Memory,-name:IO,-name:NetIO,-name:Sampling Interval,-replaying full log,-Begin of full log,-End of full log,-force-killed,-return code" --list --limit 120` (inside a zoom)
- Expected: markers minus those whose text contains the bare terms, or an error saying a bare exclusion needs a `field:` prefix.
- Got: `0 markers (filtered from 6634)`, "No markers match the specified filters". The same search with only the `-name:` exclusions returned 4799 markers.
- Workaround: listed with only `-name:` exclusions into a file and grepped the rest out.
