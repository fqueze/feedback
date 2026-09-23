## Default session directory not writable in the sandbox (browser_markup_load_01.js)

- Command: `PROFILER_CLI_SESSION_OWNER=browser_markup_load_01.js profiler-cli load <taskcluster url> --session browser_markup_load_01.js-1`
- Expected: the profile loads.
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`, then `Unknown session`.
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` on every command (other agents already use that directory). The brief and `profile-link.py` do not mention the variable; `profile-link.py` does honour it.

## Question: "at what millisecond did each of the test's log lines happen?"

- Command: `profiler-cli thread markers --category Test --search browser_markup_load_01 --list --limit 0`
- The list prints `t=2m42s` for every row, so the ~20 lines that happen within 130 ms (navigate, DOMContentLoaded, popupshown, the failure) are indistinguishable. I had to script over `--json` (`start`) to order them against IPC markers.
- What would have answered it: millisecond timestamps in `--list` rows (e.g. `t=162.455s`), at least when zoomed or when many rows share a second.

## Question: "which JS code dirtied style in this window?"

- Command: `profiler-cli thread markers --search SetNeedStyleFlush --list --limit 0` then `marker info <60 handles> --json`
- The list shows only `SetNeedStyleFlush ✓`; to see that the inspector ran `#destroyMarkup` but never `#initMarkupView` I scripted over `marker info --json` to print the first JS frames of each stack.
- What would have answered it: a `--list` option printing the top JS (non-native) frame of each marker's stack, or `--group-by` top JS frame.

## `profile markers --search` does not match a DOMEvent's target text

- Command: `profiler-cli profile markers --search 'contextmenu - ,DOMContentLoaded - document,...'`
- Expected: the DOMEvent markers whose rows read `contextmenu - body@...`.
- Got: none of the DOMEvent rows; the other terms matched.
- Workaround: `thread select <content thread>` then `thread markers --search DOMEvent --list --json` and filter the description myself.

## `--session` before the subcommand is rejected with a misleading hint (review-browser_markup_load_01.js)

- Command: `profiler-cli --session review-browser_markup_load_01.js-1 zoom push 162.44,162.60`
- Expected: the zoom applies to that session, or an error saying `--session` goes after the subcommand.
- Got: `error: unknown option '--session' (Did you mean --version?)`.
- Workaround: put `--session` at the end: `profiler-cli zoom push 162.44,162.60 --session <id>`.
