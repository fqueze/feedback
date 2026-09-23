## Question: "which test was running when this marker fired in a per-test profile?"
- Command: `profiler-cli profile markers --search nsAccessibilityService` on a per-test profile (parent main-thread markers before the last 2 s were dropped), then `profile meta` on it and on the job's resource-usage profile, subtracting the two `Started:` wall-clock times by hand (37.341 s), then `thread markers --search browser_settings.js` in the resource-usage session at the shifted time.
- Expected: a way to put two profiles of the same job on one time base (e.g. `--absolute-time`, or `zoom push --wallclock 04:57:43.36`), or print absolute timestamps next to relative ones.
- Got: relative times only; manual arithmetic across two sessions.
- Workaround: manual offset from `profile meta` "Started".

## `load` selected a content-process thread by default
- Command: `profiler-cli load <per-test profile URL> --session ...` then `thread markers --search ... --list`.
- Expected: the parent-process GeckoMain selected, as with the other per-test profiles.
- Got: `t-13 (GeckoMain, WebExtensions)` was selected; the first query silently answered for the wrong process (only the header line said so).
- Workaround: `thread select t-0` after every load.

## (review) `--session` rejected before the subcommand
- Command: `profiler-cli --session <id> zoom push 4.890,5.060`
- Expected: the session option accepted in global position, like `--version`.
- Got: `error: unknown option '--session' (Did you mean --version?)`.
- Workaround: put `--session <id>` after the subcommand.
