## `thread markers --list` prints times at 1 s resolution in a long profile

- Command: `profiler-cli thread markers --session <s> --category Test --search browser_formless_submit_chrome --list --limit 0`
- Question: in which order did the test's assertion, the doorhanger being shown, and the IPC message arrive, all within the same 40 ms?
- Expected: a start time precise enough to order markers (ms, or relative to the zoom start).
- Got: every row reads `t=2m48s` for a profile 2m49s long, so 150 markers within one second are indistinguishable in time.
- Workaround: `--json` and a script printing `flatMarkers[].start` with ms precision. The default output could show `168.179s` (or ms relative to the first listed marker) once the listed markers span less than a few seconds.

## Marker stacks for many markers need one `marker info --json` per handle

- Command: `profiler-cli marker info m-517..m-556 --session <s>` (text), then one `marker info <h> --json` per handle in a loop.
- Question: which of these 40 SetNeedStyleFlush markers came from `promptToSavePassword`?
- Expected: a compact one-line-per-marker view of the top JS frames, or `thread markers --list --search SetNeedStyleFlush` able to filter on a stack frame name.
- Got: the range form prints full multi-line stacks with native frames, ~20 lines per marker.
- Workaround: loop over `marker info --json` and print the first JS frames per marker.

## `load` selected the WebExtensions GeckoMain as the default thread of a retry profile

- Command: `profiler-cli load https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/d5UIptAQSy69pQHMQexLwg/runs/0/artifacts/public/test_info/profile_browser_formless_submit_chrome-2.js.json --session <s>`
- Expected: the parent process main thread selected, as for the other two profiles loaded in the same investigation.
- Got: `Selected thread: t-21 (GeckoMain, WebExtensions)`, so `thread markers --category Test --search <test>` returned 0 markers, which reads like "the test logged nothing".
- Workaround: `profile info --search "Parent Process"`, then `thread select t-0`. An empty result on a non-parent thread could say that Test markers live on the parent main thread.
