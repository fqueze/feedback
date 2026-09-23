## Question: in what order, to the millisecond, did the log lines of one test come? (resource-usage profile)
- Command: `profiler-cli thread markers --session <s> --search test_url_parsing --list --limit 0`
- Expected: timestamps precise enough to order events that are tens of ms apart ("exiting test" at 276.765, cache warnings at 276.836, assertion at 276.908).
- Got: every row shows `t=4m37s`, and rows are not in time order within that second (m-12 after m-59 but m-29 printed before the stack lines m-5..m-8 that came later).
- Workaround: `--json` and a python script sorting `flatMarkers` by `start`. The list could print ms (`t=276.836s`) when the view is longer than a few seconds, or when several rows share the same rounded time.

## Question: which process/thread emitted this C++ warning? (resource-usage profile)
- Command: same `--list`.
- Expected: the originating thread (`StreamTrans #3` vs `Main Thread`) in the row, since it is in the payload and is what distinguishes the cache-IO warnings from main-thread ones.
- Got: only in `marker info` (Fields: Thread) or `--json`.
- Workaround: `--json`, reading `fields[].key == 'thread'`.

## `✓ has stack` on cppDebug markers whose stack is empty
- Command: `profiler-cli marker stack m-12 --session <s>` (C++ warning marker in a resource-usage profile, listed with ✓).
- Expected: a stack, or no ✓ in the list.
- Got: `[1] unknown!null`.
- Workaround: none; relied on the printed `#NN:` stack lines replayed as `output` markers.

## Question: was the machine saturated at time t? (resource-usage profile)
- Command: `profiler-cli counter list`, `profiler-cli profile info`.
- Expected: the machine CPU use shown somewhere in the overview.
- Got: `No counters in this profile.` and `CPU activity over time: No significant activity.`; the data is only in `CPU Use` interval markers, which the list prints without their percentages.
- Workaround: `thread markers --search "name:CPU Use" --list --json` and a script printing start/end/cpuPercent/idle_pct.
