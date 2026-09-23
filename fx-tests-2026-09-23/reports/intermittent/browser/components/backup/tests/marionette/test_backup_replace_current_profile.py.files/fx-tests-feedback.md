## Same marionette gaps as in test_backup_selectable_to_legacy.py.files/fx-tests-feedback.md

- `fx-tests test browser/components/backup/tests/marionette/test_backup_replace_current_profile.py`: "No test path in the xpcshell and mochitest 21-day data contains ...". No rates or per-config runs for a marionette test. Workaround: Treeherder `/api/project/<repo>/jobs/?job_type_name=...` counts.
- `fx-tests task Or3Wbs0FR42iu7J3fes4NA --profiles`: "0 tests, 0 executions, 0 failing ... This profile records no tests at all", with a warning naming the FAIL marker. Workaround: `profiler-cli thread markers --search backup/tests/marionette --list` on the resource-usage profile.
- `fx-tests intermittent --bug 2070379`: 18 on trunk; Treeherder `failuresbybug?tree=all` has 50 since 2026-09-08, 12 of them on mozilla-beta and 4 on try, and nothing in the output says other trees were left out.
