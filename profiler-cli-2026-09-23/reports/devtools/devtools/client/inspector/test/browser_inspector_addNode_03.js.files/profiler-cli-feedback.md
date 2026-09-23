## Question: how busy was the machine during a 5 s window of the resource-usage profile?

- Commands: `profiler-cli profile info` (says "CPU activity over time: No significant activity."), `profiler-cli counter list` ("No counters in this profile."), `profiler-cli thread markers --search "name:CPU Use" --list --limit 0` after `zoom push 129.3,129.8`.
- Expected: the machine CPU % per interval, or a summary (avg/max) over the zoomed range, like `counter info` gives for Process CPU in a Gecko profile.
- Got: the `CPU Use` markers listed with only a time and a duration; the `cpuPercent` field is not printed.
- Workaround: `--json` and a Python script bucketing `fields[cpuPercent]` per 5 s.
- What would have answered it: `profile info` / `counter info`-style "over time" summary built from the `CPU Use` markers of a resource-usage profile, or the `cpuPercent` value on each listed row.

## Question: where does a per-test profile's t=16.946 s fall in the resource-usage profile?

- Command: `profiler-cli thread markers --search "name:test" --list --limit 0` on the 28-minute resource-usage profile.
- Expected: start times precise enough to align two profiles (ms).
- Got: `t=1m59s`, `t=2m7s` — rounded to the second once the profile is over a minute long.
- Workaround: `marker info m-44 --json` for `start` in ms, then subtract the same test's start in the per-test profile.

## (review) `zoom clear` / `zoom pop` back to the full profile leave sample queries on the last range

- profiler-cli 0.9.0. Commands: `zoom push 18.43,23.52`, then `zoom clear` (or `zoom push … ; zoom pop`), then `thread samples` / `thread samples-top-down` / `thread functions --search X` on the Renderer.
- Expected: samples over the whole profile (1095 running samples, what an explicit `zoom push 0,23.622` gives).
- Got: the header says `View: Full profile` and `status` says `View range: Full profile`, but the samples are still those of the popped range (494 samples, all futex). `--search mesa_Scissor` / `libgallium` then says "No samples matched" although those frames exist. Markers are not affected (`thread markers` counts all 4906).
- Cost: a wrong answer that looks right ("100% futex over the full profile", "no Mesa frames"); caught only because it contradicted an earlier result.
- Workaround: always `zoom push 0,<end>` instead of relying on `zoom clear` before a samples query.
