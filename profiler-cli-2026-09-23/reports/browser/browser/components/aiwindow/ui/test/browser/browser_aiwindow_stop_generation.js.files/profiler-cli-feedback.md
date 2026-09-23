## `thread markers --list` rounds times to whole seconds on a long profile

- Command: `profiler-cli thread markers --category Test --search browser_aiwindow_stop_generation --list --limit 0 --session browser-stop_generation-1` (6m38s profile)
- Question: when, to the millisecond, did the test submit and when did the wait time out? (The 5.2 s gap between `typeInSmartbar: query complete` and the failure is the whole story.)
- Expected: times with ms precision (e.g. `386.499s`), since test log markers are ms apart.
- Got: `t=6m26s`, `t=6m27s` ... for every row; `marker info` also printed `Time: 6m32s`. Order within a second and durations between steps are unreadable.
- Workaround: `--json` and read `.flatMarkers[].start`.

