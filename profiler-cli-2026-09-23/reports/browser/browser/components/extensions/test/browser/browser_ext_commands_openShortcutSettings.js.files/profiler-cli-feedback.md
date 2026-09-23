## Question: when did a periodic marker start and stop?

- Command: `profiler-cli thread markers --search "name:RefreshDriverTick waiting for paint" --list --limit 0` (3189 rows)
- Expected: the first and last occurrence and the gaps in a run of periodic markers, to see that a refresh driver started ticking at one test and never stopped.
- Got: either aggregates (count only) or the full list.
- Workaround: script over `--json` to find first/last and gaps > 100 ms.
- Could show: first/last timestamps and the largest gaps in the aggregate view per marker name.

## Question: which document is innerWindowID X?

- Command: `profiler-cli marker info m-1547` shows only `innerWindowID: 4294967338`.
- Expected: the page URL for that inner window (the profile's pages table has it).
- Workaround: script over `thread markers --search name:nsRefreshDriver --json` to find a marker with the same innerWindowID whose text names the URL.
- Could show: resolve innerWindowID to the page URL in `marker info`, and accept `--search innerWindowID:<n>`.

## (review) A session loaded from a profiler link cannot build links

- Command: `profiler-cli load '<profiler.firefox.com/from-url/...?marker=339827&thread=0>' --session S`, as `review-brief.md` says, then `profile-link.py --session S --marker m-4120`
- Expected: a link for a marker found while checking the report, from the session the brief told me to load.
- Got: `A link needs the raw artifact URL: load the Taskcluster URL itself, not a local copy or a profiler link.`
- Workaround: load the same profile a second time from the raw Taskcluster URL, and find the marker again (handles differ between daemons).
- Could show: `profile-link.py` extracting the artifact URL from a `from-url/` profiler link, which it already contains.
