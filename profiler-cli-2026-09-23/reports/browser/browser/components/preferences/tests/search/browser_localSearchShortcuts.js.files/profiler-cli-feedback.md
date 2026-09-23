## Review: a session loaded from a report's profiler link cannot produce links

- Command: `profiler-cli load "https://profiler.firefox.com/from-url/<artifact>/marker-table/?marker=68288&thread=0&v=17" --session S`, then `python3 profile-link.py --session S --marker m-9004`
- Expected: a link, since review-brief.md says to check a report by loading its links, and new observations found while checking need links too.
- Got: `A link needs the raw artifact URL: load the Taskcluster URL itself, not a local copy or a profiler link.` (exit 1).
- Workaround: stop the session and load the raw Taskcluster artifact URL again (another 0.6 GB load and its wait) just to link two markers. profile-link.py could take the artifact URL from the `from-url/` part of the loaded link.

## Excluding noisy runnables from a marker list

- Command: `profiler-cli thread markers --search "name:Runnable,-LocalizationRc,-ProgressTracker" --list --limit 0`
- Question: which non-trivial tasks ran on the main thread right after a long idle task (thousands of `LocalizationRc::format_messages` / `imgCancelRunnable` runnables bury them).
- Got: the bare `-term` exclusions had no effect, so the list was still all l10n runnables; `-name:` would exclude every Runnable. I scripted over `--json` to drop those labels and collapse repeats.
- Could have shown: an exclusion on the marker label (e.g. `-label:LocalizationRc`), or collapsing consecutive identical labels into "xN".
