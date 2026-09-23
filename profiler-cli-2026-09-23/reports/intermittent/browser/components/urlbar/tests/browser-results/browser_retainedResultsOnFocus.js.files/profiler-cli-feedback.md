
## review-browser_retainedResultsOnFocus.js (profiler-cli 0.9.0)

- Command: `profiler-cli --session <id> thread markers --search X --list`
  Expected: `--session` accepted as a global option before the subcommand, as `load ... --session` suggests.
  Got: `error: unknown option '--session' (Did you mean --version?)`.
  Workaround: put `--session <id>` after the subcommand's arguments.
- Command: `profiler-cli load 'https://firefox-ci-tc.services.mozilla.com/.../profile_browser_retainedResultsOnFocus-2.js.json' --session <id>`
  Question: "what did the parent main thread log?" A plain Taskcluster URL, with no `thread=` in it, selected `t-22 (GeckoMain, Privileged Content)`, not the parent GeckoMain.
  Got: three `--search` queries that returned 0 markers and looked like real absences.
  Workaround: `thread list`, then `thread select t-0`. Defaulting to the parent process main thread, or printing the selected thread's process more loudly on load, would have prevented it.
