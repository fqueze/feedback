## `zoom clear` leaves the sample range zoomed

- Command: `profiler-cli zoom push 104.130,104.152 --session S`, then `profiler-cli zoom clear --session S`, then `profiler-cli thread samples --session S` / `thread functions --search X` / `thread samples-top-down --search X`.
- Expected: samples over the full profile (6335 running samples on this thread).
- Got: header says `View: Full profile` and `status` says `View range: Full profile`, but every samples command still counted only the 2 samples of the cleared zoom (`thread functions` said "filtered from 20"). A `--search` for a function then reported "No samples matched", which reads as evidence of absence.
- Workaround: `zoom push 0,<end>` then `zoom pop` restored the full range (6335 samples).

## Checking a report's marker link and time needs a script over `marker info --json` (review-browser_jsonview_save_json.js)

- Question: "is this the marker at index N, and is it at t=104.197 s as the report says?"
- Command: `profiler-cli marker info m-11 --session S` on a 1m45s profile; also `thread markers --category Test --search X --list`.
- Expected: the time to the millisecond, and the marker index a `marker=N` link uses.
- Got: `Time: 1m44s` with no sub-second part, and no marker index. In the list, every Test 3 marker reads `t=1m44s`, so their order within that second cannot be read. Past one minute, the default output drops sub-second precision.
- Workaround: `marker info <m> --json | python3 -c ...` to print `markerIndex` and `start`, for each marker.
