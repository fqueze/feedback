## Bare `-term` exclusion in `thread markers --search` is silently ignored
- Command: `profiler-cli thread markers --session tspl-1 --search "Runnable,-DummyEvent" --list --limit 0`
- Expected: Runnable markers without the DummyEvent ones, or an error saying a bare `-term` is not an exclusion.
- Got: every DummyEvent row still listed (hundreds), no warning.
- Workaround: `--search "Runnable,-name:DummyEvent"` (the payload key of Task markers is `name`).

## Question: "in what order, and how far apart, did these markers that all print as t=1.513s happen?"
- Command: `profiler-cli thread markers --session tspl-1 --search "Runnable,-name:DummyEvent" --list --limit 0`
- The list prints `t=1.513s` for two runnables and `t=1.519s`/`t=1.520s` for the next ones, so the sub-millisecond ordering between a timer runnable, a storage completion and an idle dispatch (the whole race) needed a script over `marker info --json` to read `start`.
- What would have answered it: `--list` printing times with sub-ms precision (e.g. `t=1512.80ms`) when zoomed into a range under a second, or a `--precise-times` flag.

## Empty samples tree gives no hint it is empty
- Command: `profiler-cli zoom push 1.512,1.522 --session tspl-1; profiler-cli thread samples-top-down --session tspl-1 --include-idle`
- Expected: "0 samples in this range" (the thread has only 28 samples over 1.6 s).
- Got: a header `Top-Down Call Tree:` and nothing under it.
- Workaround: `thread info` shows the sample count for the whole thread.

## Default session directory not writable in the sandbox
- Command: `PROFILER_CLI_SESSION_OWNER=... profiler-cli load <url> --session test_selectable_profiles_lifecycle.js-1`
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`, with a clear hint to set `PROFILER_CLI_SESSION_DIR`.
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` on every call (profile-link.py honours it). Cost one command; the hint was good.

## `-name:<term>` exclusion is a substring match, and silently hides the marker under study (review)
- Command: `profiler-cli thread markers --session review-lifecycle-1 --search '-name:DummyEvent,...,-name:task,...' --list` (zoomed on 1.512–1.583 s), meant to drop the single `task` marker (`test_SelectableProfileLifecycle`).
- Expected: only markers whose name is exactly `task` excluded, or a way to ask for an exact match.
- Got: `Runnable callback[DeferredTask.sys.mjs]:JS` disappeared too, because its `name` payload contains "Task". That runnable is one side of the race, so the list suggested the notify timer never fired. No warning that the exclusion matched more than one marker name.
- Workaround: a separate `--search DeferredTask` query. What would have helped: an exact-match form (`name:=task`) for exclusions, or a count per exclusion term in the header.
