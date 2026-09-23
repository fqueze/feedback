## Question: what was the machine's average / peak CPU use while one test ran?

Command (resource-usage profile, zoomed on the test's `test` marker):

    profiler-cli zoom push m-47 --session <s>
    profiler-cli thread markers --session <s> --search "name:CPU Use" --list --limit 0 --json \
      | jq '[.flatMarkers[].data.cpuPercent | rtrimstr("%") | tonumber] | {avg: (add/length), max: max}'

Expected: `profile info` or `thread markers` (aggregated view) to summarise the `CPU Use` markers
in the zoomed range (avg / max / time above 90%), since that is the "was the machine saturated
when the timeout hit" question the resource-usage profile exists for.

Got: `profile info` says "CPU activity over time: No significant activity." for the
resource-usage profile (its CPU data lives in `CPU Use` markers, not samples), and the aggregated
`thread markers` only gives duration stats for `CPU Use`. Needed the jq script above over 3110
markers.

## Question: how many cores did each process use over a range? (review-test_group_checkerboarding.html.bug2070393)

Command: `profiler-cli counter info c-15 --session <s>` (GPU process `Process CPU`), and the same
for the parent and content processes.

Expected: an absolute figure (cores, or CPU ms per wall ms) per bucket and over the range, so
processes can be compared with each other and with the machine's core count.

Got: `90%`, `1.7%`, `0.3%`... Reading `src/profile-query/formatters/counter-info.ts` shows each is
relative to that counter's own peak rate in the range (`getMaxRangeCounterSampleCountPerMs`), so
the percentages of two processes are not comparable, and "90%" does not mean 90% of a core or of
the machine. The report under review compared them as if they were. `--json` has only the
normalised `graph` array too.

Workaround: downloaded the raw profile and summed `counters[].samples.count` (ns of CPU) over
`timeDeltas` in Python: GPU process 7.5 cores of 8, every other process under 0.01. Could have
shown: "avg 7.51 cores (peak 8.18)" next to the percentage.
