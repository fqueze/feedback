## Question: "at what time, to the millisecond, did this marker happen?"
- Command: `profiler-cli thread markers --session <s> --category Test --search <test> --list --limit 0`
- Expected: timestamps precise enough to order events and quote them in a report (for example t=288.699s).
- Got: `t=4m49s`, rounded to the second, which is not enough to order markers that are close together.
- Workaround: `profiler-cli marker info m-16 m-43 ... --json`, reading `.start` with a Python one-liner.

## `screenshots --at` image too small to read page text
- Command: `profiler-cli screenshots --session <s> --at 294.5 -o <dir>`
- Expected: to read the two status lines of the test page (service-worker-error vs pending).
- Got: a 350px-wide, 4.6 KB JPEG. The text is unreadable, and the CompositorScreenshot capture is probably that small already.
- Workaround: none. I did not rely on it.
