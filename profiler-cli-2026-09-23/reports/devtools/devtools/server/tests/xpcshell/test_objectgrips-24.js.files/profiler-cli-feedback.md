## Question: "which `test` markers with status PASS *started* after time T?"

- Command: `profiler-cli zoom push 131.676,999` then `thread markers --search status:PASS --list --limit 0`.
- Expected: the tests that started after T, here 0: to show that no test launched after an msix package breakdown passed.
- Got: every interval marker that overlaps the zoom, including the tests that started long before T and ended after it, so the list does not answer the question. I had to script over `--list --json` and filter `start > T`.
- What would answer it: a `--starts-in` (or `--overlap=contained|starts|any`) mode for `thread markers` under a zoom.

## Small: `marker info --json` records have `handle: null`; `stop` accepts one id only

- `profiler-cli marker info m-1 m-3 --json`: each record's `handle` is null, so the records cannot be matched to the handles asked for except by order.
- `profiler-cli stop <id1> <id2>`: `error: too many arguments for 'stop'`. One call per session works.
