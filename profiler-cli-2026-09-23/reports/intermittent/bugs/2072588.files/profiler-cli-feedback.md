# profiler-cli feedback (bug 2072588)

## Question: "what was the machine's average CPU / iowait / disk write rate over this time range?"
- Command: `profiler-cli thread markers --session 2072588-1 --search "name:CPU Use" --list --json` (resource-usage profile of a wdspec job)
- Expected: a way to aggregate a numeric marker field over the current zoom (mean/max of `iowait_pct`, sum of `write_bytes`), or at least the fields in the `--list --json` rows.
- Got: `--list --json` rows carry only handle/label/start/duration, no payload fields; `marker info` on an `IO` marker prints no fields at all (the IO/Memory/NetIO payloads have no schema, so `write_bytes`/`read_bytes` are not shown, not even under "Other payload fields").
- Workaround: downloaded the profile JSON and summed the marker payloads with a Python script (ru_io.py / ru_sum.py in this directory). For resource-usage profiles this is the main question (machine saturated or not), so a `counter`-like view of the CPU/IO markers per range would answer it directly.

## `marker info` on CPU Use prints the "Other payload fields" with wrong-looking values
- Command: `profiler-cli marker info m-1 --session 2072588-1`
- Got: `user: 0.276, system: 0.276, iowait: 0.276, softirq: 0.276` (all equal), while the schema fields above them show different percentages (User 27.4%, IO Wait 0.0%).
- Expected: the raw values as stored (they differ in the JSON: e.g. `user: 0.025, system: 0.025, iowait: 0.087, softirq: 0.1` for another marker). Looks like the first value is repeated.

## Correction to the item above ("Other payload fields" repeated values)
- Withdrawn: the raw JSON payload itself has `user`, `system`, `iowait` and `softirq` equal (0.276 for m-1; they look like stacked graph values), so `marker info` prints them faithfully. Not a profiler-cli bug.

## (review-2072588) `profile-link.py` refuses a session loaded from a profiler.firefox.com link
- Command: `profiler-cli load "<profiler.firefox.com/from-url/...marker-table/?marker=3014&thread=0&v=17>" --session review-2072588-1`, then `profile-link.py --session review-2072588-1 --marker m-188`
- Expected: a link, since the session knows the underlying artifact URL (it is inside the from-url link it loaded). The review brief says to load the report's links to check them, so reviewers load exactly this kind of URL.
- Got: "A link needs the raw artifact URL: load the Taskcluster URL itself, not a local copy or a profiler link."
- Workaround: loaded the same profile a second time from its Taskcluster URL (another ~0.6 GB session) just to build links. Unwrapping the `from-url/` part of a profiler link would avoid the reload.
