## Question: did the test whose skip-if this try push removes fail on it, and where are its profiles?

- Command (as given in the subagent brief): `fx-tests try 9db28f6db8bf1b77ad7ed9de11211a54450dfc61 --profiles --task-ids`
- Expected: the test's row, with its task IDs and profile URLs.
- Got: nothing for `netwerk/test/unit/test_udp_multicast.js`. It fails 8 of 8 runs on the push, but it sat under `… 41 more (--limit 0 for all)` of the 51 perma-fails, behind 10 mochitest rows. A grep of the output for the test name matched nothing, which reads as "did not fail".
- Workaround: rerunning with `--limit 0` (a second full read of 143 job profiles) found it. `--test <path>` gives the per-config answer directly, but its per-config table has no task IDs or profile URLs, so the profile URLs still needed `--limit 0` or `fx-tests task`.
- Could have shown: with a single test in mind, `--test <path> --profiles --task-ids` printing that test's failing task IDs and profile URLs. Separately, the brief's command for the skipped-test case could use `--test <path>` or `--limit 0`.
