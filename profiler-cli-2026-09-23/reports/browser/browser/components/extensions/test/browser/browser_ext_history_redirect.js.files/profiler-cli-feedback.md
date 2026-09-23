## Question: is a periodic marker continuous from one moment to the end of the profile

Command: `profiler-cli thread markers --search "RefreshDriverTick waiting for paint" --list --limit 0 --json` piped to python to compute first/last time and the largest gap after t=6.8s.
Expected: the aggregate view (`thread markers --search X`) to give first and last occurrence times and the largest gap between consecutive markers of a name (instant markers only get a count).
Got: count only; the "Frequency Analysis" section is not printed for this marker in the per-name view, and --list prints 5440 rows.
Workaround: --json and a script.
What the output could show: first/last timestamps and max inter-marker gap per marker name in the aggregate view.

## Review (browser-review-browser_ext_history_redirect.js): profile-link.py refuses sessions loaded from a report link

Command: `profiler-cli load '<profiler.firefox.com/from-url/...?marker=N&thread=0>' --session S`, as review-brief.md says to check a link, then `python3 profile-link.py --session S --marker m-9051`.
Expected: a link, since the session holds the same Taskcluster profile.
Got: "A link needs the raw artifact URL: load the Taskcluster URL itself, not a local copy or a profiler link." (exit 1).
Workaround: stop the session and reload the raw Taskcluster URL, costing a second download and load per profile.
Suggestion: profile-link.py could extract the from-url target from a profiler.firefox.com link, or review-brief.md could say to load the raw URL when new links will be needed.

## Question: which document does a periodic refresh-driver marker belong to

Command: `thread markers --search "waiting for paint" --list --limit 0 --json` then a script counting `data.innerWindowID`, then `marker info <DocumentLoad handles> --json` to map the ID to a URL.
Expected: `thread markers --search X --group-by innerWindowID` (or the list) to show the window's URL, as the profiler UI does from the page list.
Got: the innerWindowID is only in the JSON, and never resolved to a URL.
What the output could show: the URL of the inner window next to markers that carry one.
