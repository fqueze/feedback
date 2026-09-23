## Default session dir not writable in the sandbox
- Command: `PROFILER_CLI_SESSION_OWNER=... profiler-cli load <taskcluster url> --session browser_firstPartyIsolation_aboutPages.js-1`
- Expected: the profile loads.
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`, then `Unknown session` on the next command.
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` on every call (the error message suggested it, which helped). One wasted load.

## Question: "when exactly did this interval marker end, relative to that instant marker"
- Command: `profiler-cli thread markers --search about:welcome --list --limit 0`
- Needed: whether the about:welcome DOMWindow/DocShell markers end before or after `Completed ShutdownLeaks collections in process <pid>` (they are ~0.5-1.5 s apart).
- Got: `t=6m10s  46.555s` — start rounded to the second, so the end has to be computed and is only good to ~1 s. Had to run `marker info --json` and read `start`/`end` in a script.
- Could have shown: start and end with ms precision in `--list` (at least when the view spans minutes), or an `--end` column.

## `marker info --json` records carry no handle
- Command: `profiler-cli marker info m-263 m-265 ... --json`
- Expected: each record names the handle it answers for.
- Got: no `handle` key (my script printed `None`), so records have to be matched by order.

## `--category Test` finds nothing in a resource-usage profile
- Command: `profiler-cli thread markers --category Test --search browser_firstPartyIsolation_aboutPages --list --limit 0` on `profile_resource-usage.json`
- Expected: the test's log (as the diagnose brief suggests for per-test profiles).
- Got: `0 markers`; the test/PASS/INFO markers there are in category `Other`. Dropping `--category` works, but then the search also matches every DOMWindow/DocShell marker whose payload carries the test name (262 hits).
