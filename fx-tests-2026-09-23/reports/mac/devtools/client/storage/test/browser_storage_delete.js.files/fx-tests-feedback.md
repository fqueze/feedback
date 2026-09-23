## Question: "does any bug name this test?"

- Command: `fx-tests test devtools/client/storage/test/browser_storage_delete.js --bugs`
- Expected: a Bugs section, or a line saying none was found.
- Got: exactly the same output as without `--bugs`; `--json` had `annotatedBugs: []` and no `bugs` key. I could not tell whether it searched and found nothing or ignored the flag. A Bugzilla summary search found only resolved single-tracking bugs and the umbrella bug 2044571, whose summary does not name the test.
- What would have answered it: an explicit "Bugs: none naming this test (searched open and closed)" line.

## Resource-usage profile had no CPU tracks

- Command: `profiler-cli load https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/XvztFRm3TVmLPZsJ_7yqAw/runs/0/artifacts/public/test_info/profile_resource-usage.json` then `profiler-cli counter list`.
- Expected (per the fx-tests guide, "the one showing whether a timeout was the test being slow or the machine saturated"): machine CPU tracks.
- Got: one thread, 0 ms CPU, "No counters in this profile". Only the `test` and log markers. So "was the machine saturated" could not be answered for this macOS job.
