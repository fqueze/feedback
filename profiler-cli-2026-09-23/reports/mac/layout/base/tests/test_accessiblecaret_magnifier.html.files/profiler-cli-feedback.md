## Default session directory not writable in the sandbox

- Command: `PROFILER_CLI_SESSION_OWNER=... profiler-cli load <taskcluster URL> --session test_accessiblecaret_magnifier.html-1`
- Expected: the profile loads.
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`, followed by `Unknown session ...` on the next command. The error does suggest `PROFILER_CLI_SESSION_DIR`.
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` on every command. The subagent brief does not mention it, and `profile-link.py` needs the same variable to find the session.

## Question: "which document does this DOMEvent target?"

- Command: `profiler-cli thread markers --search DOMEvent --list`
- Output shows `mouselongtap - document` with no hint of which document (outer test document or inner iframe document). Deciding it took reasoning from neighbouring markers. If the marker payload carries the target's document URL or node address, printing it would have answered it directly.

## Review: same session-directory EPERM; `review-brief.md` does not mention it either (review-test_accessiblecaret_magnifier.html)

- Command: `PROFILER_CLI_SESSION_OWNER=review-... profiler-cli load <link> --session review-...-1`
- Got: the same `EPERM ... mkdir '/Users/florian/.profiler-cli'`. Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli`.

## Question: "which thread handle is this link's `thread=c`?" (review-test_accessiblecaret_magnifier.html)

- A report links markers on several threads (`thread=7`, `c`, `b`, `l`). To check them from one load, I had to work out by hand that `c` is `t-12` and `l` is `t-21`. `thread list` could show each thread's URL `thread=` value, or `load` could accept a link's `thread=` value in `thread select`.

## `marker info` JSON shape changes with the number of handles (review-test_accessiblecaret_magnifier.html)

- Command: `profiler-cli marker info m-1428 m-1429 --json`
- Expected: the same shape as for one handle, in a list. Got: `{"type": "marker-info-multi", "markers": [...]}`, while one handle returns the bare object. So a script written for one handle breaks silently on two.
