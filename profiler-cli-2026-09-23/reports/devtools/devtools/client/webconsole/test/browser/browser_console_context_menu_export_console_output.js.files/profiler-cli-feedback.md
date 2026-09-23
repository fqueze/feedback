## Markers named exactly ERROR

- Question: how many markers named `ERROR` (the harness's TEST-UNEXPECTED-FAIL lines) does this job have?
- Command: `profiler-cli thread markers --session review-ctxexport-1 --search "name:ERROR" --list --limit 0`
- Expected: the 9 `ERROR` markers.
- Got: 1039 markers (172 KB), because `name:` does a substring match and also hits `console.error`, `JavaScript error`, and Text payloads.
- Workaround: ran `rg` over the saved output for `^\s+m-\d+\s+ERROR\s`.
- Could show: an exact-name filter, such as `name:=ERROR`.
