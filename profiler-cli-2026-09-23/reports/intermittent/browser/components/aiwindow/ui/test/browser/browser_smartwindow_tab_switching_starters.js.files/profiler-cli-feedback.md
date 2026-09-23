## Question: at what precise time did each line of the test's log happen?

- Command: `profiler-cli thread markers --category Test --search <test file> --list --limit 0 --session <s>`
- Expected: timestamps precise enough to order steps that are tens of ms apart (e.g. `t=16m2.200s`).
- Got: `t=16m2s` on every row once the profile is over a minute long; the four steps of the failing sequence (961.8 s, 962.12 s, 962.20 s, 962.201 s) all read `16m2s`. `marker info` has the same rounding (`Time: 16m2s`).
- Workaround: `--json` and a Python script over `flatMarkers[].start`.
- What the output could show: milliseconds in `--list` rows (at least when the list spans less than a few seconds, or always for instant markers).

## `zoom push` accepts a range entirely outside the profile

- Command: `profiler-cli zoom push 962143,962192 --session <s>` (I passed ms instead of seconds; profile is 963 s long)
- Expected: an error ("range 962143 s–962192 s is outside the profile, 0–963 s"; maybe "did you mean ms?").
- Got: success, `status` then showed `View: ts>10X→ts>10y (49s)`, and every query returned "0 markers in view" as if the window were empty.
- Workaround: noticed the 49 s view, `zoom pop`, re-pushed in seconds.

## Question: was the main thread idle or busy over this window?

- Command: `profiler-cli thread samples-top-down --include-idle --session <s>` over a 94 ms zoom.
- Expected: a line such as "9 samples, 0 idle" or an idle node, so "busy for the whole window" can be read directly.
- Got: a tree whose root children are task labels; with no native frames there was no visible idle marker, and I had to go through `--json` (`regularCallTree` → children of `XREMain::XRE_main`) to count samples per task and spot the one `_GI___poll` sample.
- What the output could show: sample count and idle/busy split in the header of every samples command.

## (review) `marker stack` on a `setTimeout callback` does not say the stack is the setTimeout call's

- Command: `profiler-cli marker stack m-301 --session <s>` (marker at 962183.6, "setTimeout handler with interval 50ms")
- Expected: a note that this is the cause stack (where `setTimeout` was called), with the time it was captured.
- Got: `Captured at: 16m2s` and a stack topped by `Window.setTimeout ← getAiWindowElement/< ... ← nsTimerImpl::Fire`, i.e. from the *previous* timer fire, 140 ms earlier. It reads as the stack of the callback itself.
- What the output could show: "Cause stack (setTimeout called at 962044.4 ms)". I also needed a script over `--json` again for ms-precise `--list` times (same question as the first entry above).
