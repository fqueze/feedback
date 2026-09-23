## The machine's CPU use over the seconds around a failure, in a resource-usage profile

- Commands: `profiler-cli counter list` ("No counters in this profile"), `profile info` ("CPU activity over time: No significant activity"), then `zoom push 29.5,31.0` + `thread markers --search "name:CPU Use" --list --limit 0`.
- Expected: the CPU percentage of each 100 ms `CPU Use` marker in the list, or a counter/sparkline over the zoom.
- Got: the list rows have an empty label, so the value only shows with `marker info` one marker at a time; `profile info` says "No significant activity" for a profile whose whole point is the machine's CPU.
- Workaround: `--json` and a script printing `fields[0].value` per marker.
- What it could have shown: a `CPU Percent` column in the `--list` rows for `CPU Use` markers (or those markers exposed as a counter).
- Also: the default session directory `~/.profiler-cli` is not writable in this sandbox; `load` says so and suggests `PROFILER_CLI_SESSION_DIR`, which worked.

## Which DOM events of one type fired, in order, next to other markers

- Command: `thread markers --search "MenuOpened,MenuClosed,willenter,willexit,sizemodechange,fullscreen - window,View:FullScreen" --list --limit 0`
- Expected: the `DOMEvent fullscreen - window` rows along with the others, since that is the text the list prints for them.
- Got: every other term matched, but no `fullscreen - window` row: the printed label is built from eventType and target and is not searchable as one string. A bare `fullscreen` term would match every fullscreen-related marker and preference.
- Workaround: an unfiltered `--list` inside a narrow `zoom push`, grepped.
- What it could have shown: search matching the printed label, or a documented `eventType:fullscreen` example for DOMEvent markers in `thread markers --help`.
