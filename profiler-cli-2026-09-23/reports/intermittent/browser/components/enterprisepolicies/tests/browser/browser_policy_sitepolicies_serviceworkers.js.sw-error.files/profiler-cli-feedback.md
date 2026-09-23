## Marker list times lose sub-second precision past 60 s

- Question: "how long after the test entered did perm-changed fire, and which test was running at that moment?"
- Command: `profiler-cli thread markers --session sw-error-1 --search perm-changed --list` (and the same with `--category Test --search Entering`)
- Expected: times like `t=60.194s` or `t=1m0.194s`.
- Got: `t=1m`, `t=1m9s` for every marker past 60 s, so several markers in the same second (test boundaries, perm-changed) could not be ordered.
- Workaround: `profiler-cli marker info m-132 m-519 ... --json` and a python script reading `.start`.

## `load` selects a content process thread by default

- Command: `profiler-cli load <per-test mochitest profile URL> --session sw-error-3`
- Expected: the parent process GeckoMain selected, as for the other per-test profile loaded the same way.
- Got: `t-29 (GeckoMain, Privileged Content)` selected. The next `thread markers --search perm-changed` quietly searched the wrong thread and found 1 marker instead of 4.
- Workaround: `profile info --search "Parent Process"`, then `thread select t-0`.

## `--session` rejected before the subcommand (review)

- Command: `profiler-cli --session review-sw-error-1 thread markers --search test_pageinfo_cookie_per_site_policy --list --limit 0`
- Expected: `--session` accepted as a global option, as `load … --session` suggests.
- Got: `error: unknown option '--session' (Did you mean --version?)`.
- Workaround: put `--session` after the subcommand's own options.
