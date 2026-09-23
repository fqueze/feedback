# profiler-cli feedback (review-overscroll-behavior.html.bug2070403)

## Question: was the machine busy while this test ran (resource-usage profile)?

- Command: `profiler-cli profile info --session <s>` on JDUFtT-2RHiaNOuWLTgr1w's `profile_resource-usage.json`
- Expected: some sign that machine-wide CPU use is recorded, since the profile has `CPU Use` markers every 100 ms.
- Got: "CPU activity over time: No significant activity." and a single thread with 0 ms CPU. The original report concluded from this that the profile "has no CPU tracks".
- Then: `thread markers --search "name:CPU Use" --list` prints one row per marker with no value; `--json` of the list has no payload either.
- Workaround: `marker info <27 handles> --json` and a script to pull `cpuPercent` (10-27% during the test). A `--list` column with the main payload field (here `cpuPercent`), or a CPU line in `profile info` built from these markers, would answer it directly.
