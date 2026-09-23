## Question: which markers were caused by a given JS function (here, which style flush came from `adjustHeight`)?

- Command: a shell loop running `profiler-cli marker stack m-N` over every `SetNeedStyleFlush` marker in a 20 ms zoom (100 calls), grepping each stack for `adjustHeight`.
- Expected: a way to filter `thread markers` by a frame in the marker's own stack, e.g. `thread markers --search SetNeedStyleFlush --stack-search adjustHeight --list`.
- Got: `--search` matches name, category and payload only, not the stack; one call per marker was the only way, slow and noisy.
- Workaround: the loop above; in the end the answer came from reading the stack of the single marker in the refresh tick.
- What the output could show: a stack filter on `thread markers`, or the leaf JS frame of the marker's stack as a column in `--list`.

## Session directory not writable in a sandbox

- Command: `PROFILER_CLI_SESSION_OWNER=... profiler-cli load <url> --session <id>`
- Expected: a load.
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'` — the message itself named the fix (`PROFILER_CLI_SESSION_DIR`), so this cost one call; noting it because `profile-link.py` must then see the same variable, and the brief does not mention it.
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` on every `profiler-cli` and `profile-link.py` command.

## review-browser_popup_mouseover.js: `--session` only accepted after the subcommand

- Command: `profiler-cli --session <id> thread markers --category Test ...`
- Expected: the global option to work before the subcommand, as `--session` is on every call.
- Got: `error: unknown option '--session' (Did you mean --version?)`.
- Workaround: put `--session <id>` at the end.

## review-browser_popup_mouseover.js: searching the label a DOMEvent row shows matches nothing

- Command: `profiler-cli thread markers --search "overflow - richlistbox,mousemove - autocomplete" --list --limit 0 --session <id>`
- Expected: the rows displayed as `overflow - richlistbox@…` and `mousemove - autocomplete-row-item@…`.
- Got: no match, because the label joins two payload fields (event type and target) and `--search` matches each field on its own.
- Workaround: search `overflow,mousemove`, then filter the output with `rg`.
- What the output could show: let `--search` match the displayed label as well, or say in `--help` that a label made of several fields cannot be searched as one string.
