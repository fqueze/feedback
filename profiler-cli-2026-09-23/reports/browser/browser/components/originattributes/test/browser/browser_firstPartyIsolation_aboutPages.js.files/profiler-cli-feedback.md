## Question: "did interval marker A end before or after instant marker B" (both minutes into the profile)

- Command: `profiler-cli marker info m-713 m-1156 --session ...` and `thread markers --list`
- Expected: times precise enough to order events ~1 s apart (a DOMWindow's end vs the "Completed ShutdownLeaks collections in process N" output line).
- Got: `Time: 6m10s - 6m56s` / `t=6m55s` — rounded to the second past one minute, so the two could not be ordered.
- Workaround: `marker info ... --json` and reading `start`/`end` in ms.
- What could have shown it: millisecond precision in `marker info` (or a `--precise` flag), and/or an `end` column in `--list`.

## `thread markers --search "2,148"` (a pid as shown in labels) matches 63,892 of 105,030 markers

- Command: `profiler-cli thread markers --session ... --search "2,148" --list`
- Expected: markers from process 2148 (the label shows `[Child 2,148: ...]`).
- Got: most of the profile (CPU Use, Memory, NetIO, Phase...), since the comma splits the term into "2" OR "148".
- Workaround: `--search pid...` did not occur to me; used `--json` and filtered on `data.pid`. A note in `--help` that the comma in formatted numbers is the OR separator would have saved the detour.
