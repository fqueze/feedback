## `fx-tests task --profiles` misses a profile the failure message names

- Command: `fx-tests task I-gIIwQpSfKfw1KOSL5NIQ --profiles`
- Expected: the `public/test_info/profile_startup_content_mainthreadio.json` artifact listed for
  `browser_startup_content_mainthreadio.js`, since the test's own failure message says "open the
  profile_startup_content_mainthreadio.json artifact in the Firefox Profiler".
- Got: "No failing test named a per-test profile in this job." and only the resource-usage profile.
- Workaround: listed the task's artifacts with the Taskcluster queue API and loaded that URL.
- Question it could have answered: "which profiles did this failing test upload?" — any
  `profile_*.json` artifact named in a failure message, not only the harness's
  `profile_<test>.json` convention.

## `fx-tests test --bugs` prints nothing for a moved test, and says nothing

- Command: `fx-tests test browser/base/content/test/browser-performance/browser_startup_content_mainthreadio.js --bugs`
- Expected: the bugs naming the test (2071154, 2071550), or a line saying none were found under
  this path and that the bugs may name its previous path.
- Got: the normal output with no bugs section at all; `--json` has `"annotatedBugs": []`. The
  same command on the old path (`.../test/performance/...`, renamed 2026-09-18 in bug 2069131)
  returns both bugs.
- Workaround: ran it on the old path.
