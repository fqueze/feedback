## Question: which tests were running when the job broke down?

- Command: `profiler-cli thread markers --session <s> --search name:test --list --limit 0 --json` then a Python script to list `test` markers overlapping a time window and bucket their statuses by start second.
- Expected: a way to list interval markers overlapping a point in time (e.g. `--overlapping 44.4`) or a `--group-by field:status` histogram over time.
- Got: only a chronological list sorted by start; long-running tests started 12 s earlier are far away in the list.
- Workaround: `zoom push <t-1>,<t>` does not help either, since it keeps markers by overlap but the list is still 1000+ rows in a mass-failure job; used the JSON.
- Correction to the entry above: I did not actually try `zoom push` for this; disregard that sentence.
