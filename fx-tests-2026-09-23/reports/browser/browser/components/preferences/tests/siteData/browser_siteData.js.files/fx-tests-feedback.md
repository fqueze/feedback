## Which bug are these failures starred on?
- Command: `fx-tests test browser/components/preferences/tests/siteData/browser_siteData.js --bugs`
- Expected: a Bugs section listing bug 1438751, on which sheriffs starred 5 of these failures in the last 7 days (`fx-tests intermittent --bug 1438751` confirms it).
- Got: the normal output with no Bugs section and no "none found" line.
- Workaround: Bugzilla REST search on the summary, then `fx-tests intermittent --bug <N>` on each candidate.

## Timeout jobs have a hang profile that `task --profiles` does not list
- Command: `fx-tests task K6S_hyetR068xA0ztYWskQ --profiles`
- Expected: the harness's hang profile `public/test_info/profile_0_5185.json` (533 MB, written after "application timed out after 370.0 seconds with no output").
- Got: "No failing test named a per-test profile in this job."
- Workaround: listed the task's artifacts with the Taskcluster API.

## A passing job of the same config, to compare its resource-usage profile
- Question: does the machine-wide write burst seen in failing jobs also occur in jobs where the test passed, or that do not run it?
- Got: `fx-tests test` only lists failing task IDs.
- Workaround: Treeherder `api/jobs/?push_id=` for sibling jobs of the failing push.
