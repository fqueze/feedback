## `zoom push` with minute suffixes is silently read as seconds
- Command: `profiler-cli zoom push 26.2m,26.7m --session <s>`
- Expected: zoom to 26m12s-26m42s, or an error about the unit.
- Got: `View: ts-1→ts-2 (500ms)` near the profile start, then "No markers match" for queries that do match at 26m.
- Workaround: `zoom push 1578,1600` (seconds). The marker lists print times as `26m29s`, so the minute form is natural to type back.
