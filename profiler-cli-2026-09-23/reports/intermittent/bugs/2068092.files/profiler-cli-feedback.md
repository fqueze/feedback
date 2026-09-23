## `zoom push 1m50s,1m56s` silently zooms to a 0 s range at 1 s

- Command: `profiler-cli zoom push 1m50s,1m56s --session 2068092-1` (the `1m52s` format is what
  `thread markers --list` prints for marker times).
- Expected: a zoom to 110 s - 116 s, or an error saying the format is not accepted.
- Got: "Pushed view range: ts-1 (1s) to ts-1 (1s) (duration: 0s)", and the next query ran on a
  1 ns view without complaint.
- Workaround: `zoom push m-59` (the test marker), or seconds.

## Question: what was the machine's CPU use during this test?

- Command: `profiler-cli thread markers --search "name:CPU Use" --list --limit 0` after
  `zoom push m-59` on a resource-usage profile.
- The list shows name, start and duration only; the CPU percent needed `--json` (or one
  `marker info` per handle).
- What would have answered it: the `cpuPercent`/`idle_pct` fields in the `--list` rows for `CPU`
  markers, or a `counter`-style summary of the CPU markers over the zoomed range.
