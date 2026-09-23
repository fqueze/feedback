## Question: in what order did these events happen within one second?

- Command: `profiler-cli thread markers --session <s> --search "eventType:keydown,eventType:click,eventType:focus,eventType:blur" --list --limit 0`
- Expected: timestamps precise enough to order the rows. I was reconstructing a focus race where the events are 1–5 ms apart.
- Got: each row shows `t=17m10s`, so every event in that second looks the same. The start time is not shown to the millisecond, and there is no end time for interval markers.
- Workaround: `--json | jq '.flatMarkers[] | "\(.handle)\t\(.start/1000)\t\(.duration)\t\(.label)"'`, run on every list I needed. That was about 10 calls in this investigation.
- What would have answered it: once a zoom is narrower than a few seconds, or with a flag, `--list` could show `t=` with milliseconds (e.g. `1029.8711s`) plus the duration.

## Default thread after load was a content process

- Command: `profiler-cli load <taskcluster per-test profile URL> --session <s>`
- Expected: the parent process GeckoMain selected, as the main thread of a mochitest browser-chrome profile.
- Got: `Selected thread: t-119 (GeckoMain, Privileged Content)`.
- Workaround: `thread select t-0`.

## Question: what did the AI window look like just before the Enter? (review)

- Command: `profiler-cli screenshots --session <s> --at 1030.07 -o <dir>`
- Expected: one image per window at that time.
- Got: it wrote 2 of the 3 windows, then aborted with `Error: Screenshot for m-8773 is not a base64 data URL, so it cannot be written to a file.` The one that failed was the window under investigation. `marker info m-8773` shows `url: "825038fca2c567f30"`, which looks like an unresolved string index. `--json` fails the same way.
- Workaround: `--range 1029.99,1030.09`, which also aborts but writes the earlier frames first. Then `marker info` on each to find the latest good frame of window 726.
- What would have answered it: skip the bad frame with a warning, and fall back to that window's previous good frame.
- Also hit again: the ms-precision `--list` timestamps question above. Needed `--json` scripts for all four profiles.
