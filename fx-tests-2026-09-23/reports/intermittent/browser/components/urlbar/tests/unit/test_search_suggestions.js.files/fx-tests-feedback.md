## Question: what timeout did this test run under on this config?

- Command: `fx-tests test browser/components/urlbar/tests/unit/test_search_suggestions.js --durations`
- Expected: next to the per-config passing durations, the effective timeout (30 s x the config's timeoutfactor x the test's requesttimeoutfactor), so "median 28.1 s against a 30 s limit" reads at a glance.
- Got: durations only. A 95 s median on macosx1015 debug next to a 30 s-limit failure on macosx1500 looked contradictory until I read `taskcluster/kinds/test/xpcshell.yml` (timeoutfactor 6 there, default 1 on macosx1500-aarch64).
- Workaround: read the harness constant and the taskcluster timeoutfactor by hand.
