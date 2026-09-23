## Marker times past one minute lose sub-second precision (also logged by test_animation_name.js)

- Command: `profiler-cli thread markers --session test_addons_actor.js-2 --search test_addons_actor --list --limit 0`, then `marker info m-1 m-3`
- Expected: start times to the millisecond, to compare a test's start with a failure 1.3 s earlier.
- Got: `t=2m13s` in both the list and `marker info` (`Time: 2m13s - 2m58s`).
- Workaround: `marker info m-1 m-3 --json` and read `start` (132983.908).
