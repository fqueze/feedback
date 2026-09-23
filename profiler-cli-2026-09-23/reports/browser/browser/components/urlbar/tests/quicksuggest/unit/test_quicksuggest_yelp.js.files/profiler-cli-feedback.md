## Question: "was the machine saturated while this test ran" on a resource-usage profile

- Command: `profiler-cli profile info --session S` and `profiler-cli counter list --session S` on `.../profile_resource-usage.json` (task fBYgkLcuSA-yxbgFnjcGQg).
- Expected: the whole-machine CPU track, as the brief says the resource-usage profile has.
- Got: "CPU activity over time: No significant activity." and "No counters in this profile." The data is there as `CPU Use` markers every 100 ms with `cpuPercent` and `iowait_pct` fields, which neither command surfaces.
- Workaround: `zoom push <test marker>`, then `thread markers --search "name:CPU Use" --list --limit 0 --json` and a Python script to average `cpuPercent`/`iowait_pct` per 5 s. What would have answered it: `profile info` (or `counter info`) summarising the CPU Use / IO markers over the zoomed range, like it does for real counters.

## Question: "what is the last marker of this filtered list"

- Command: `profiler-cli thread markers --search "name:TEST-PASS" --list --limit 0`
- Expected: a way to see the tail (e.g. `--last N` / `--reverse`), to know how far the test got before being killed.
- Got: only the head is shown by default; `--limit 0` prints all 1,400 rows.
- Workaround: `--json` and taking `flatMarkers[-1]` in Python.

## (review) Question: "how did these requests fail, and how long did each failure mode take"

- Command: `profiler-cli thread network --session S --search mochi.test --limit 0`
- Expected: each request's `requestStatus` (`NS_ERROR_UNKNOWN_HOST`, `NS_BINDING_ABORTED`), and counts and durations grouped by it.
- Got: `canceled` for the aborts and `???` for the DNS failures (e.g. `m-69 ... ???`); `--json` has `status: STATUS_STOP/STATUS_CANCEL` but no `requestStatus`.
- Workaround: `marker info` on all 112 handles, `--json`, and a Python group-by. What would have answered it: print `requestStatus` per request and in the summary.

## (review) `marker info --json` changes shape with the number of handles

- Command: `profiler-cli marker info m-34 --json` vs `profiler-cli marker info m-13 m-32 --json`
- Expected: one shape.
- Got: a bare marker object for one handle, `{markers: [...]}` for several; the script written for one broke on the other.

## (review) Question: "which marker is `marker=N` in this link"

- Command: none exists; `marker info` takes handles only.
- Expected: a way to look up a marker by `markerIndex`, to check a link.
- Got: the `Starting prefsDisabled` INFO marker was 15383, not the linked 15385; found the `task` interval marker by guessing.
