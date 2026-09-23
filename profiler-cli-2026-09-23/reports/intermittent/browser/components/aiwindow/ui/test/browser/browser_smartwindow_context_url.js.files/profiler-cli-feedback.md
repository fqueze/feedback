## Question: "in what order, to the millisecond, did these test-log lines and these network requests happen?"
- Command: `profiler-cli thread markers --category Test --search <test> --list --limit 0` (and the same for `chat/completions`).
- Expected: start times precise enough to order events a few ms apart, since a race is decided at that scale.
- Got: `t=33m55s`, rounded to the second. I had to script over `--json` (`flatMarkers[].start`) to get ms. `--list` could show ms when the view is zoomed, or offer a `--precise-times` flag.

## Question: "is a Network marker's payload startTime in the same timebase as the marker list?"
- Command: `profiler-cli marker info m-108` (a Network marker) next to the `--list --json` start of the same marker.
- Got: the payload `startTime`/`requestStart`/`responseStart` fields were 10.8 ms (one profile) and 22.8 ms (another) later than the list start of the same marker. The payload's raw times are not in the profile-relative timebase, and nothing says so. I compared them against test-marker list times, and at first got the order wrong.
- Expected: `marker info` shows the phase times converted to the list's timebase, or labels them as raw.

## Question: "when were the samples in function X taken?"
- Command: `profiler-cli thread functions --search generateConversationStartersSidebar` gives counts but no times.
- Workaround: a shell loop of `zoom push a,b` + `thread functions --search` over 250 ms windows, then 20 ms windows (about 50 invocations).
- Expected: something like `thread samples --search X --list-times`, or the sample time ranges for a function.

## (review) The unconverted Network payload times led to a wrong number in the report
- The payload-vs-list timebase offset logged above caused a real error even after the author knew about it. The report said "sent 11 ms after the assertion", taking the payload `startTime` of Load 2974 (2035561.6) minus the list time of the assertion (2035550.2). Converted, it is 0.6 ms (start) and 4 ms (`requestStart`). A reviewer only catches this by redoing the subtraction.
- Also: `marker info m-N --json` for a Network marker does not put `startTime`/`requestStart`/`responseStart` in `fields[]` (they came back `None`), while the text output lists them under "Fields (raw payload, no schema)". I had to rerun without `--json` and grep.
