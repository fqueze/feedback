## Were any samples idle in this range?

- Command: `profiler-cli thread samples --session <s> --include-idle` after `zoom push 32.553,32.669` on the parent main thread.
- Expected: the number of samples in the range, idle ones included, so that "busy the whole time" can be told apart from "busy for some of it".
- Got: `Categories (7 running samples)`, with no Idle row. On another thread the same flag did print an `Idle` row. So an absent Idle row seems to mean "no idle samples", but the header says "running", and 7 samples in 116 ms at a 10 ms interval looked short. I could not tell whether samples were missing or were idle and dropped.
- Workaround: took it as no idle samples, since the same command lists `Idle` when there is some.
- Suggestion: print the total sample count in the range and the idle count, even when it is 0.

## (review) When was this RefreshDriverTick's stack captured, and of what?

- Command: `profiler-cli marker stack m-181 --session <s>` on the parent main thread of Cav-SV1sQ3miOVutBRqZUg, for the `RefreshDriverTick` interval 32.6697–32.6721.
- Expected: a stack from inside that tick, or a label saying it is a cause stack captured earlier.
- Got: `Captured at: 32.532s`, with a `Reflow browser.xhtml` stack, which is 138 ms before the marker starts and inside the previous tick. Nothing says whether it is this tick's cause or a mismatch.
- Workaround: ignored it and used the stacks of `SetNeedStyleFlush` markers inside the tick instead.
- Suggestion: say "cause stack, captured at …" when the capture time lies outside the marker's interval.

## (review) Loading a bare Taskcluster URL selected the WebExtensions thread

- Command: `profiler-cli load 'https://firefox-ci-tc.services.mozilla.com/.../profile_browser_ext_incognito_views.js.json' --session <s>`.
- Expected: the parent process main thread selected, as when loading the profiler.firefox.com link with `thread=0`.
- Got: `t-35 (GeckoMain, WebExtensions)` selected; the next `thread markers` listed its markers, which I first read as the parent's.
- Workaround: `thread select t-0`.
- Suggestion: default to the parent main thread (the `load` output does name the selected thread, but a test profile's reader expects the parent).
