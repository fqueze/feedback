## Question: which tests were running at time t, and how did outcomes change over time? (resource-usage profile)

- Command: `profiler-cli thread markers --session <s> --search "name:test" --list --limit 0`, then a Python script over the text output to (a) bucket test statuses by start time and (b) list tests whose interval contains a given timestamp.
- Expected: a way to ask for markers overlapping an instant (e.g. `--at 50.35` or `zoom push` semantics restricted to markers spanning the instant), and a `--group-by field:status` over time buckets.
- Got: `zoom push a,b` lists markers overlapping the range (good), but no way to get "spanning t" or a status-by-time histogram; had to parse the text, where durations print inconsistently (`1m1s` vs `1m`), which broke my first parser.
- Workaround: script over the text list.
## review-test_DominatorTreeNode_insert_01.js: `--category Test` is empty on a resource-usage profile

- Command: `profiler-cli thread markers --session S --category Test --list --limit 0 --json` on `profile_resource-usage.json`.
- Expected: the `test` / INFO markers (subagent-brief.md suggests `--category Test`).
- Got: 0 markers, with no hint; the categories there are Other, Tasks, Phases.
- Could show: the thread's available categories when a `--category` filter matches nothing.
