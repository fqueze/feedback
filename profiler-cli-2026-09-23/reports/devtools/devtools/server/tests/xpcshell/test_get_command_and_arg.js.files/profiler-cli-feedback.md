## Marker times past one minute lose sub-second precision in list and info output

- Command: `profiler-cli thread markers --session test_get_command_and_arg.js-2 --search test_get_command_and_arg --list --limit 0`, then `profiler-cli marker info m-1 m-3 m-7 --session test_get_command_and_arg.js-2`
- Question: how many ms after the test started did the harness log `will retry`, and how long after the first 0x80073CFC launch failure did the test start?
- Expected: `t=133.203s` / `2m13.203s`, as markers under 60 s get (`t=46.544s`).
- Got: `t=2m13s` in the list and `Time: 2m13s (instant)` in `marker info`, for both markers 34 ms apart.
- Workaround: `marker info ... --json` and read `start` in ms.
