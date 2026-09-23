# profiler-cli feedback

## Question: the millisecond order of the harness's DEBUG markers inside one 135 ms reftest
- Command: `profiler-cli zoom push m-723 --session text-layout-10.svg-2; profiler-cli thread markers --session text-layout-10.svg-2 --list --limit 0` (a reftest job's resource-usage profile, test marker at 84 s)
- Expected: start times precise enough to order the markers of one test (`AfterPaintListener`, `MakeProgress`, `AttrModifiedListener fired`), which are 0 to 5 ms apart.
- Got: every marker past the first minute prints `t=1m24s`, so all ~45 markers of the test show the same time. Before 60 s it prints `t=41.364s`, which is enough.
- Workaround: `--json` and `flatMarkers[].start`, through a script (`timeline.sh` in this directory), for 30 jobs.
- What the output could have shown: times relative to the zoom start, or seconds with milliseconds whatever the magnitude (`t=84.231s`).
