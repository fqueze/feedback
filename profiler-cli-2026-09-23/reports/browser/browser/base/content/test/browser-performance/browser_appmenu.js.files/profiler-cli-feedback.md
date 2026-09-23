## Question: "which markers were emitted from inside function X" (e.g. the SetNeedStyleFlush caused by SearchModeSwitcher.updateSearchIcon)

- Command: `profiler-cli thread markers --search updateSearchIcon --list` → 0 markers; `--search` only matches name/payload, not the marker's stack.
- Workaround: `thread markers --search name:SetNeedStyleFlush --list --limit 0 --json`, then a shell loop of `marker stack m-N` over all 219 handles (one daemon call each, since `marker stack` refuses more than one handle while `marker info` takes lists and ranges), then grep. ~5800 lines of scratch output.
- Could have shown: a `--stack-search <frame substring>` filter on `thread markers`, and multi-handle / range support on `marker stack` like `marker info`.

## (review) `profile-link.py` refuses a session loaded from a report's profiler link

- Question: "link a new observation in a profile I opened from the report's link", as `review-brief.md` says to do (`profiler-cli load <link>`).
- Command: `profiler-cli load "https://profiler.firefox.com/from-url/…/marker-table/?marker=41707&thread=0&v=17" --session S`, then `python3 profile-link.py --session S --marker m-1085`.
- Expected: a link, since the from-url wraps the Taskcluster artifact URL.
- Got: `A link needs the raw artifact URL: load the Taskcluster URL itself, not a local copy or a profiler link.`
- Workaround: stop the session and reload the raw Taskcluster URL (another ~0.6 GB load; marker handles change). profile-link.py could unwrap the `from-url/` URL itself, or the review brief could say to load the raw URL.
