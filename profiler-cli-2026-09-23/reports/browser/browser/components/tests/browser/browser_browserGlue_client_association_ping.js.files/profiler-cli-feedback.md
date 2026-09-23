## Question: "was the main thread ever free between t1 and t2" (could an idle task have run?)

- Command: `profiler-cli thread markers --session <s> --search 'name:Runnable' --list --limit 0 --json` after `zoom push 1.588,1.867`, piped to a Python script merging the Runnable intervals and printing the gaps longer than 3 ms.
- Why: the diagnosis hinged on idle-priority tasks never running between two points; the default list output is 489 rows with no way to see where the thread was between runnables.
- Could have shown: a per-range "busy/free" summary for a thread (gaps between Runnable/task markers, and which of them were inside Awake markers or sync-IPC markers such as HandleMainThreadCATransaction), or a `--gaps <ms>` option on `thread markers --list`.
