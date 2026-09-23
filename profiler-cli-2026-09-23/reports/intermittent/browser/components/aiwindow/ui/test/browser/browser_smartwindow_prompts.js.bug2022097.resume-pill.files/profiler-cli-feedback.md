## Question: in what order did these markers happen, a few ms apart, late in a long profile?

- Command: `profiler-cli thread markers --category Test --search browser_smartwindow_prompts --list --limit 0 --session <s>` (and the same with `--search smartbar-initialized,quickPromptDisplayed,...`) on an 8-minute profile.
- Expected: a timestamp precise enough to order markers that are 0.1-2 ms apart (the diagnosis hinged on `TEST-UNEXPECTED-FAIL` at 482.1938 s vs the render at 482.1946 s).
- Got: `t=8m2s` for every row; `marker info` also prints `Time: 8m2s`. Even inside a 243 ms `zoom push`, rows still show `t=8m2s`.
- Workaround: `--list --json` piped to a Python script printing `start/1000` with 4 decimals. The text output could show milliseconds (e.g. `482.1938s`, or relative to the zoom start) whenever the list spans less than a few seconds or rows share the same rounded time.

## (reviewer) `--session` placed before the subcommand is rejected

- Command: `profiler-cli --session review-resume-pill-1 thread markers --list --limit 0`
- Expected: the global option accepted anywhere, as `load ... --session` suggests.
- Got: `error: unknown option '--session' (Did you mean --version?)`.
- Workaround: put `--session` after the subcommand. The error could say where it goes.

## (reviewer) Same question as above: marker order at sub-ms precision

- Needed `--list --json` and a script again, on five profiles, to order `load`, `smartbar-initialized`, the test's read and the render, 0.5-2 ms apart. Rows showed `t=8m2s` / `t=16m30s` throughout.
