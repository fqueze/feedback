## Marker times past one minute lose sub-second precision (same as test_animation_name.js's entry)

- Question: how many ms after its start did this test give up, and how long after the job's first launch failure did it start?
- Command: `profiler-cli thread markers --session test_stepping-11.js-1 --search test_stepping-11 --list --limit 0`, then `marker info m-1..m-6`
- Expected: `t=65.733s` for the start and `t=65.775s` for `will retry`.
- Got: `t=1m6s` for both, in the list and in `marker info` (`Time: 1m6s - 1m51s (45.044s)`).
- Workaround: `--list --json | python3` reading `start`.
