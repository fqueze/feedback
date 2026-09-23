## C++ warning markers flagged as having a stack, stack is empty (bug 2050667)

- Command: `profiler-cli thread markers --session 2050667-1 --search nsAHttpConnection --list --limit 0`, then `profiler-cli marker stack m-143 --session 2050667-1`
- Profile: resource-usage profile of task enwj9ZxfR96A07xMrKB3_A (`public/test_info/profile_resource-usage.json`).
- Expected: the ✓ ("has stack trace") to mean a usable stack.
- Got: ✓ on every `C++ warning` marker, but `marker stack` prints only `[1] unknown!null`.
- Workaround: none; ignore the ✓ on markers converted from log lines.
