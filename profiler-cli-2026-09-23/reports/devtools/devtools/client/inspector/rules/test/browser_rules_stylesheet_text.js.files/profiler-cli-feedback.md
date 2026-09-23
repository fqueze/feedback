## Marker times have no sub-second precision in long profiles

- Command: `profiler-cli thread markers --category Test --search browser_rules_stylesheet_text.js --list --limit 0 --session <s>` and `profiler-cli marker info m-220 --session <s>` on a 12m21s per-test profile.
- Expected: times with ms precision (the question was "how long after the 0.5 s wait did the assertion run", a sub-second gap).
- Got: `t=11m41s` in the list and `Time: 11m41s (instant)` in marker info, so markers 865 ms apart print the same time.
- Workaround: `marker info ... --json` and read `.start` with a python one-liner.
- Also: `zoom push m-220,m-277` is refused ("Expected timestamp name (ts-X), seconds ..."), though `zoom push m-158` works for one interval marker; a range between two instant markers needs their JSON starts.

## (review) `--session` is not accepted before the subcommand

- Command: `profiler-cli --session review-browser_rules_stylesheet_text.js-1 profile info`
- Expected: the session applied, as with a global option.
- Got: `error: unknown option '--session' (Did you mean --version?)`.
- Workaround: put `--session` after the subcommand (`profiler-cli profile info --session <s>`).
- Also hit again: sub-second marker times (above). Every precise comparison in this review needed `marker info m-a..m-b --json` piped to python for `.start`.
