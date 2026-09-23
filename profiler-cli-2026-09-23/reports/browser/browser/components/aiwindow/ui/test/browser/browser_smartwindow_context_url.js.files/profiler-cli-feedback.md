## Question: "at what millisecond did each of these markers happen?" (long profile)

- Command: `profiler-cli thread markers --session browser-ctxurl-1 --category Test --search browser_smartwindow_context_url.js --list --limit 0`
- Expected: timestamps precise enough to order events that are 10-500 ms apart.
- Got: in a 32-minute profile every row reads `t=31m56s`, `t=32m`, `t=32m1s`. Events 10 ms apart (the assertion before the clear, then the HTTP request) cannot be ordered.
- Workaround: `--json` piped into a Python script that printed `start/1000` with 3-4 decimals. I did this for every marker list in the investigation.
- What the output could show: seconds with ms precision (e.g. `t=1920.480s` or `t=32m0.480s`) when the view is long, or a `--time-format` flag.

## Question: "when was function X sampled?"

- Command: `profiler-cli thread functions --search loadStarterPrompts --session ...` (also `samples-top-down --search`)
- Expected: the times of the samples containing the function, to put it before or after a marker.
- Got: only counts and percentages, with no time.
- Workaround: a shell loop of `zoom push a,b` / `thread functions` / `zoom pop` over 10 ms buckets (about 20 round trips per question).
- What the output could show: a `--list`-style mode for samples matching `--search`, with each sample's time, or the first and last sample time per function.

## Question (reviewer): "link a new observation in the profile I opened from the report's link"

- Command: `profiler-cli load '<profiler.firefox.com/from-url/... report link>' --session S` (as review-brief.md says to), then `python3 profile-link.py --session S --marker m-522`
- Expected: a link to the marker, built from the loaded profile.
- Got: `A link needs the raw artifact URL: load the Taskcluster URL itself, not a local copy or a profiler link.` Empty output.
- Workaround: `profiler-cli stop` the session and reload the raw Taskcluster artifact URL (another ~0.6 GB load) just to build links.
- What could work: profile-link.py (or profiler-cli) extracting the from-url artifact URL from a profiler.firefox.com link, since the review brief tells reviewers to load those links.
