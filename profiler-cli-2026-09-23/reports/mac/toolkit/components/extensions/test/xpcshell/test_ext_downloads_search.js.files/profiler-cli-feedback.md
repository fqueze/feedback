## Question: "what wall-clock (epoch) time did this marker happen at?"

- Command: `profiler-cli marker info m-14 --session <s>` (and `profile meta`)
- Expected: a way to see a marker's absolute time, or at least the offset between the `t=` shown and `meta.startTime`.
- Got: only profile-relative `t=`. Converting needs `meta.startTime` + `context.rootRange.start` (`profilingStartTime`) + the marker's `start` from three separate `--json` outputs, and working out by trial that marker `start` is relative to `rootRange.start` while `currentViewRange` is not.
- Workaround: a script over `--json` (`gap.py` next to this file). What the output could show: `marker info` printing `epoch ≈ <ms>` next to `t=`, with a note that it is derived from `meta.startTime` written when the profile was captured.

## Question: "how long between marker A and marker B?"

- Command: `profiler-cli thread markers --search "TEST-PASS" --list` then `--search addedFileExtension --list`, then `marker info` on each to get sub-ms times.
- Expected: list rows at sub-ms precision, or a `marker info A B` delta.
- Got: `t=2.645s` in list rows (1 ms resolution), which cannot tell a 0.5 ms from a 1.4 ms gap.
- Workaround: `--json` and a script.

## Search misses a legacy telemetry `Event` marker's shown text

- Command: `profiler-cli thread markers --search "downloads.added" --list --limit 0`
- Expected: to match the `Event` markers listed elsewhere as `Event  t=2.645s  downloads.added#fileExtension html`.
- Got: only the `Event::record` markers (`downloads.addedFileExtension: {"value": "html"}`); the `Event` markers only matched a broader search such as `Load` (via "downLOADs").
- Workaround: searched for `addedFileExtension` instead.

## Default session dir not writable in the sandbox

- Command: `profiler-cli load <url> --session <s>`
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`; the message did suggest `PROFILER_CLI_SESSION_DIR`, which worked.
- Cost: one failed load. It would help if the brief or the tool defaulted to `$TMPDIR/profiler-cli` when home is not writable.
