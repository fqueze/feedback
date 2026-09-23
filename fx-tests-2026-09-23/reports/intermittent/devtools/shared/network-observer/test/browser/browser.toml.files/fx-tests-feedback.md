# fx-tests feedback (browser.toml, bug 2020133)

## Question: how often does this manifest's shutdown leakcheck fail, and did it stop after the fix?

- `fx-tests test devtools/shared/network-observer/test/browser/browser.toml`
  - Expected: failure rate of the manifest-level `leakcheck large nsGlobalWindowInner | <manifest>` failure (40 sheriff-annotated jobs in the window).
  - Got: "No test path in the xpcshell and mochitest 21-day data contains ...", exit 2.
- `fx-tests failures --path devtools/shared/network-observer --harness mochitest`
  - Expected: the leakcheck message attributed to the manifest.
  - Got: "No failure matched" over 2026-09-01..09-21, although ~39 such failures happened in that window.
- Manifest-attributed leakcheck failures seem to be absent from the per-test data entirely, so the only fx-tests source is `intermittent --bug` (sheriff stars), which cannot say whether an unstarred failure happened after the bug was resolved.
- Workaround: Treeherder API, `jobs/?result=testfailed&last_modified__gt=...` filtered to `debug-mochitest-devtools-chrome`, then `jobs/<id>/bug_suggestions/` grepped for the manifest (63 jobs). What would have answered it: a manifest row in `fx-tests test`, with leakcheck/shutdown results counted per manifest run.

## Question: which bug names this manifest?

- `fx-tests intermittent --test devtools/shared/network-observer/test/browser/browser.toml --since 30`
  - Expected: bug 2020133, whose summary is "Intermittent leakcheck large nsGlobalWindowInner | devtools/shared/network-observer/test/browser/browser.toml | single tracking bug".
  - Got: "no sheriff-annotated bug names the test ... between 2026-08-24 and 2026-09-22", while `--bug 2020133` over the same window lists 47 annotations.
  - Workaround: already had the bug number.

## Question: was the last occurrence before or after the fix landed?

- `fx-tests intermittent --bug 2020133 --since 30 --limit 0`
  - The occurrences table has push time, tree and task id, but no revision, so checking "does the last failing push contain the fix" needed `fx-tests task <id>` for the revision, then Treeherder's push API and a git ancestry check.
  - What would have answered it: the revision in the occurrences table, or a `--after <rev>` style filter.
