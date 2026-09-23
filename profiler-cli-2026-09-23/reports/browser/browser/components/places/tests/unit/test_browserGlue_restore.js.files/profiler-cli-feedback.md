## Bare negative term in `thread markers --search` matched nothing
- Command: `profiler-cli thread markers --search "-name:CPU Use,-name:Memory,-name:IO,-name:NetIO,-name:Sampling Interval,-name:test,-failed or timed out" --list` (inside a zoom)
- Expected: every marker except those types, and without the text "failed or timed out".
- Got: "0 markers (filtered from 170)". The same query without the bare `-failed or timed out` term worked earlier. So a bare `-term` seems to be taken as something other than an exclusion, or it is not supported, and nothing says so.
- Workaround: positive `name:INFO,name:ERROR,...` search, piped through `grep -v`.
