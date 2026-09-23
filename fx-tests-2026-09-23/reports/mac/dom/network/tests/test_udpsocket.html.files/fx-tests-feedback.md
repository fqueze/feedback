# fx-tests feedback (test_udpsocket.html)

## `try` NEW INTERMITTENTS count disagrees with `--test`, and a test that failed every run is listed as intermittent

- Command: `fx-tests try 9db28f6db8bf1b77ad7ed9de11211a54450dfc61 --profiles --task-ids` (same row with `--all-jobs`)
- Expected: `dom/network/tests/test_udpsocket.html` under PERMA-FAILS, since it failed both executions (first run and retry) of the only job that ran it, with a count matching the job data.
- Got: listed under NEW INTERMITTENTS as `2/3` in the "here" column. `fx-tests try <rev> --all-jobs --test dom/network/tests/test_udpsocket.html` shows 1 job, 0 passed, 1 failed, and `fx-tests task A4K4Dmp8SNa8LeIelC80wg` shows `2 failing executions of 2`. Nothing says what the third run in `2/3` was.
- Workaround: ran `--all-jobs --test <path>` to get the real per-config count. It re-read every job profile, which was slow.
- Question the default output could have answered: "on how many jobs/executions did this test run on the push, and did any pass?" A per-row `ran in N jobs` next to the here column would have answered it without the extra `--all-jobs --test` pass.
