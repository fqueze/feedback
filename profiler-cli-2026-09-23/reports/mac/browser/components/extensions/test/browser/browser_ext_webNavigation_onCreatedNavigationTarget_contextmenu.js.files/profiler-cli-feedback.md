## Socket path too long: the suggested fix is the directory already in use

- Command: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli PROFILER_CLI_SESSION_OWNER=browser_ext_webNavigation_onCreatedNavigationTarget_contextmenu.js profiler-cli load <taskcluster url> --session browser_ext_webNavigation_onCreatedNavigationTarget_contextmenu.js-1`
- Expected: the session loads (the skill brief asks for sessions named `<report name>-1`, and report names are test file names, often 60+ chars), or an error suggesting something that would work.
- Got: `The Unix socket path for this session is 114 bytes, over this platform's 103-byte limit` followed by "Use a shorter session directory, for example: PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli", i.e. the directory already set. The session name, which is the long part, is not mentioned as the thing to shorten.
- Workaround: a short session name (`wnCNTctx-1`) with the long name kept as PROFILER_CLI_SESSION_OWNER. A hashed/truncated socket file name would remove the limit on session names altogether.
- Also: the default session dir `~/.profiler-cli` is not writable in this sandbox (EPERM); the error message did suggest the right alternative there.

## Marker times past one minute lose their milliseconds in text output

- Command: `profiler-cli thread markers --category Test --search onCreatedNavigationTarget_contextmenu --list --limit 0 --session wnCNTctx-1` (and `marker info m-6 m-49 m-16`)
- Question: when exactly did the test body end and the harness's vsync wait begin, to zoom on that window?
- Expected: times like `t=369.461s` (or `6m9.461s`), as markers under one minute get (`t=14.061s`).
- Got: `t=6m8s`, `t=6m9s`, `t=6m18s` for every marker, in both the list and `marker info` ("Time: 6m18s (instant)"). A whole test fits in one printed second, so the order and the zoom range cannot be read off.
- Workaround: `--json` and read `.flatMarkers[].start` (ms) in a script.


## (review) `--session` is rejected before the subcommand

- Command: `profiler-cli --session review-wnctxmenu-1 thread markers --category Test --search onCreatedNavigationTarget_contextmenu --list --limit 0`
- Expected: the global option accepted anywhere, as `load ... --session X` suggests it is global.
- Got: `error: unknown option '--session' (Did you mean --version?)`.
- Workaround: put `--session` after the subcommand's own options.

## (review) Does a repeating marker run without interruption from A to B, and where are its gaps?

- Command: `profiler-cli thread markers --search 'name:RefreshDriverTick waiting for paint' --list --limit 0 --json --session review-wnctxmenu-2`, then a script over `.flatMarkers[].start`
- Question: does the orphaned popup's `RefreshDriverTick waiting for paint` run at 60 Hz from its first occurrence to the end of the profile, and are its pauses only at test boundaries?
- What the default output could have shown: the aggregate view already prints min/avg/max interval per name; the first and last timestamps, and the largest few gaps with their start times, would have answered it without a script.
