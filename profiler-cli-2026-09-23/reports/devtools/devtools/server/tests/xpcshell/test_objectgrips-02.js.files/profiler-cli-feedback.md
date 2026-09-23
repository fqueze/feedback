## Question: "when did the job-wide breakdown start, and which tests were running at that moment?" (resource-usage profile)
- Command: `profiler-cli thread markers --search name:test --list --limit 0` (1,389 rows), then `--json` and a Python script to bucket `test` markers by start time and status. The script also listed the markers overlapping a given time.
- Expected: a way to ask for the markers overlapping time T (for example `--overlapping <t>`), and a histogram of start times grouped by label prefix (PASS/TIMEOUT/SKIP).
- Got: only a flat chronological list or name-level aggregates. The aggregate's "min/avg/max duration" did not show that 475 of the TIMEOUTs started within 1.4 s.
- Workaround: `--json` plus a script (see burst.py and prebreak.py in this directory).
