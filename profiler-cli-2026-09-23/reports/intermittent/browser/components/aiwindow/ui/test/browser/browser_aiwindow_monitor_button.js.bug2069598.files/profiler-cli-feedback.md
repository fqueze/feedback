## `thread samples` on the full view counts far fewer samples than a zoom covering the same data

- Session: hang profile `profile_0_1751.json` (NILiRZR-QT6agXl6m2WsaA), samples only from 4m31s to 13m20s.
- `profiler-cli zoom clear; profiler-cli thread samples` -> "Categories (448 running samples)" (another call earlier in the same session, also "View: Full profile", said 2561).
- `profiler-cli zoom push 272,800; profiler-cli thread samples` -> "Categories (23128 running samples)".
- Expected: the full view to include at least the 23128 samples of the zoom inside it. Workaround: always zoom to the range that has samples before reading samples.

## `screenshots` says the feature is off when the profile lists it

- `profiler-cli screenshots --at 795 -o dir` on the same profile: "This profile was captured without the "screenshots" feature", while `profile meta` lists `Features: js, screenshots, ipcmessages, processcpu, memory`. The screenshot markers were probably dropped with the rotated buffer; saying so would avoid a wrong conclusion.

## (review) No way to find which marker a link's `marker=N` is, among many with the same name

- Question: which of the 353 `multiline-editor-placeholder-animation` markers is the report's `marker=1787825`?
- `profiler-cli thread markers --search multiline-editor-placeholder-animation --list --limit 0 [--json]`: neither the text nor the JSON rows carry `markerIndex`. Workaround: pass all 353 handles to `marker info --json` and filter on `markerIndex`.
- Could show: a `--marker-index N` lookup (or `marker info idx:N`), or `markerIndex` in the `--json` flat rows.

## (review) The marker list does not say an IPC message was never delivered

- Question: which IPC messages reached this process but never ran on this thread?
- `profiler-cli thread markers --search FOGData --list`: `IPCIn t=4m36s 0s`. The only sign that it never ran is `phase: transferEnd` (with no `endTime`) under "Other payload fields (no schema)" in `marker info`. In the same list, a delivered message shows its queueing time as its duration.
- Could show: the phase in the list row, or "not delivered" for an `IPCIn` that has no endpoint phase.
