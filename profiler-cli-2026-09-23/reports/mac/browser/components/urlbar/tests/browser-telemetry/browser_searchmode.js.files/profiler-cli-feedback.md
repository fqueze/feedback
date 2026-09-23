## A search term containing "word: " silently becomes a field filter

- Command: `profiler-cli thread markers --session bsm-2 --search 'priority: Idle,SearchHandoff,InitEngineStore,keydown' --list --limit 0`
- Expected: Runnable markers whose name contains "priority: Idle" (e.g. `IdleRequestExecutor - priority: Idle (0)`), alongside the other terms.
- Got: the other terms matched, the "priority: Idle" term matched nothing and nothing said so. Its absence read like "no idle task ran in this range", which was the very question being asked.
- Workaround: searched for `IdleRequestExecutor` instead. A warning when a `field:` prefix names no payload key of any marker in the thread would have caught it.
