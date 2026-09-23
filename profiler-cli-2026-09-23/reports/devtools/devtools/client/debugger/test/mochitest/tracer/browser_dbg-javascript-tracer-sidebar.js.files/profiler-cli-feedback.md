## Which of two markers came first, when both show the same millisecond

- Question: did the rIC handler (`setTimeout callback ... toolbox.js:1098`) run before or after the `TEST-UNEXPECTED-FAIL`? Both print `t=16.508s` in `thread markers --list`.
- Command: `profiler-cli thread markers --session <s> --search "Idle" --list --limit 0`, then `marker info m-1479 m-11`.
- Expected: enough precision in the list (or `marker info`) to order them: they are 0.09 ms apart (16507.897 vs 16507.807).
- Got: `t=16.508s` for both in the list; `marker info` also rounds.
- Workaround: `marker info m-1479 m-11 --json` and a Python one-liner printing `start`.
- Could have shown: sub-ms times in `marker info` text output, or more digits in `--list` when adjacent rows round to the same value.

## `thread samples-top-down --max-depth`

- Command: `profiler-cli thread samples-top-down --session <s> --include-idle --max-depth 12`
- Expected: a depth-limited tree (common need to keep output short).
- Got: `error: unknown option '--max-depth'`.
- Workaround: used `thread samples --limit 25`.

## (review) Was the main thread busy in this 28 ms window?

- Question: was the parent main thread busy between 16.4755 s and 16.5037 s (profile O0WSDJCfTSK0V5BpytOqag, sidebar test)?
- Command: `profiler-cli zoom push 16.4755,16.5037 --session <s>` then `profiler-cli thread info --session <s>`.
- Expected: a busy figure matching the markers: `Runnable` plus `Perform microtasks` markers cover 26.9 of the 28.2 ms.
- Got: `CPU activity over time: No significant activity.` The thread has very few samples there (2 in the 72 ms from 16.436 s, at a 10 ms interval), so the sample-based figure says "idle" for a busy thread. `profile info` ignores the zoom altogether and prints whole-profile CPU times.
- Workaround: `thread markers --search "name:Runnable,name:Perform microtasks" --list --limit 0 --json` and a Python union of the intervals.
- Could have shown: busy time from task and microtask markers next to the sample-based figure, or a warning when the view has too few samples to judge.
