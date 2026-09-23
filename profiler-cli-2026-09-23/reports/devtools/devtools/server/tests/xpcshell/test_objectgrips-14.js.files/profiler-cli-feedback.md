## Question: when did this test start, relative to the moment the job broke down?

- Question: "did the test start after the first failed launch in the job, and did any test pass after that point?"
- Command: `profiler-cli thread markers --search "name:test,will retry,Failed to launch,objectgrips-14" --list --limit 0 --json | python3 job.py`, where the script finds the first `will retry` / `Failed to launch` marker and counts the `test` markers starting after it, by status.
- What could have shown it: `thread markers --after m-N` (or `--after <t>`) together with `--group-by field:status`, to count the markers after a given marker by status without a script.
