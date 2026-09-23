## `profile info` says "No significant activity" for a profile with no samples and no counters

- Command: `profiler-cli profile info --session test_ext_menu_startup.js-ru1` on `https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/fHKIwkcoRSGJjDLlotw6yg/runs/0/artifacts/public/test_info/profile_resource-usage.json` (macOS 15 xpcshell resource-usage profile)
- Expected: a statement that the profile holds no samples and no counters, i.e. no CPU data exists, so "was the machine saturated?" cannot be answered from it.
- Got: "CPU activity over time: No significant activity." and `counter list` "No counters in this profile." The first reads as "the machine was idle".
- Workaround: downloaded the JSON and checked `threads[0].samples.length == 0` and `counters == []`.

## `counter info` prints 0% for a time bucket that holds no counter sample

- Command: `profiler-cli zoom push 4.300,4.420` then `profiler-cli counter info c-1 --session review-test_ext_menu_startup.js-1` (Process CPU of the parent process, profile of task fHKIwkcoRSGJjDLlotw6yg). Logged by the reviewer (session owner `review-test_ext_menu_startup.js`).
- Expected: a bucket with no sample shown as "no sample" (or the value of the sample whose interval covers it, since the counter is `line-rate`), and the sample times listed so the reader can tell.
- Got: `[4.380s - 4.400s] 0%`. The raw counter has samples at 4.3788s and 4.4024s only, and the one closing that interval is at 27% of the view's peak. Zooming to 4.340–4.405s instead prints `4.373–4.389s 98%` and `4.389–4.405s 27%` for the same data. The 0% reads as "the process was idle", and the report under review quoted it that way.
- Workaround: downloaded the profile JSON and read `counters[].samples.timeDeltas` / `count` for the parent `processCPU` counter.
- Question the default output could have answered: "what did the CPU counter measure over this interval?" — the sample times and per-sample values in range.
