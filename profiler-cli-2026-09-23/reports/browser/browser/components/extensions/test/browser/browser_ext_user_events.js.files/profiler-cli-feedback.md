## `thread markers --list` rounds times to whole seconds past one minute

- Command: `profiler-cli thread markers --session <s> --category Test --search browser_ext_user_events --list --limit 0` on a 5 min profile.
- Expected: millisecond times (`t=296.771s`), as it prints below one minute (`t=6.720s`).
- Got: `t=4m57s` for every marker of the test, so the order and gaps between the test's steps, and between two markers on different threads, cannot be read.
- Workaround: `marker info <handles> --json` and read `start`/`end`, one extra call per question.

## Default selected thread after `load` is sometimes a WebExtensions thread

- Command: `profiler-cli load <taskcluster per-test profile URL> --session <s>`, then `thread markers --category Test --search browser_ext_user_events --list`.
- Expected: the parent process GeckoMain selected, as it was for other CI profiles, or the empty result to say that Test markers exist on another thread.
- Got: `Selected thread: t-47 (GeckoMain, WebExtensions)` (and t-13 in another profile), and `No markers match the specified filters.`, which reads as "the test logged nothing".
- Workaround: `thread list`, then `thread select t-0`.
