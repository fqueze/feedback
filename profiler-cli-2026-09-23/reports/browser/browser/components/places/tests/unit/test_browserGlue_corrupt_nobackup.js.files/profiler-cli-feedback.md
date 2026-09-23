## `C++ warning` markers flagged "has stack" (✓), but the stack is empty
- Command: `profiler-cli marker stack m-97 --session …` on a resource-usage profile (xpcshell job).
- Expected: the warning's stack, since the list marks it ✓.
- Got: `[1] unknown!null`, for every C++ warning marker tried.
- Workaround: none. Only file:line is available, from `marker info`.

## CPU Use values are not in `thread markers --list --json`
- Question: "was the machine saturated during these seconds".
- Command: `profiler-cli thread markers --search "name:CPU Use" --list --json` (zoomed)
- Got: start and duration per marker, but not `CPU Percent` / `Idle %`. I had to feed every handle to `marker info`.
- What it could have shown: the payload fields (or at least CPU %) in the list/JSON rows.
