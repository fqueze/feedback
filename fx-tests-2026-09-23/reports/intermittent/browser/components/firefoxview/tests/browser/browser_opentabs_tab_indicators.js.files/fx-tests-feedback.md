## Question: "which failing runs of this test have a per-test profile?"

- Command: `fx-tests test browser/components/firefoxview/tests/browser/browser_opentabs_tab_indicators.js --profiles --limit 20`
- Expected: the per-test profile URLs, or a statement that none of the failing configs (here tsan/opt and asan/opt, 483 of 483 failures) upload any.
- Got: 20 resource-usage URLs only, then a pointer to `fx-tests intermittent --test <path> --profiles`. `fx-tests task <id> --profiles` on tsan jobs said "No failing test named a per-test profile in this job", without saying why.
- Workaround: `fx-tests intermittent --test <path> --profiles` found the only per-test profile, from a linux2404-64/debug test-verify job that sheriffs had starred on the bug. It took four commands to learn that the worst config never has one.
- What would help: `fx-tests test --profiles` could list the tasks that do have per-test profiles first, whatever the config, and say when a config never uploads them.
