## Question: are there bugs for this test? (`--bugs` output ambiguous)

- Command: `fx-tests test browser/components/translations/tests/browser/browser_translations_select_context_menu_feature_disabled.js --bugs`
- Expected: a "Bugs" section, or an explicit "no sheriff-annotated bug names this test".
- Got: exactly the same output as without `--bugs`: no section, no "none" line. "No bug" and "the lookup was skipped or failed silently" look the same.
- Workaround: took it as "none"; no bug number turned up anywhere else.

## Question: did passing runs of the same config, at the same time, run on healthy or slow workers?

- Command: `fx-tests test <path> --task-ids --limit 0` lists failing tasks only.
- Expected: some way to get a few passing task IDs of one config near a failure's time (e.g. `--task-ids --passing --config wayland/opt --day 2026-09-18`), so their resource-usage profiles can be compared.
- Got: no way to get them from fx-tests.
- Workaround: `curl "https://treeherder.mozilla.org/api/project/autoland/jobs/?job_type_name=test-linux2204-64-wayland/opt-mochitest-browser-translations&count=300&last_modified__gt=..."`, then loaded each resource-usage profile.

## Question: when did each failure run, and was it a backfill? (same as the sibling report's entry, again)

- Command: `fx-tests test <path> --task-ids --limit 0`
- Expected: the time and the Treeherder symbol for each task. 9 of the 13 were one backfill batch (`M-bk(tr8ns-b606af2b1dd-bk)`) within 40 minutes; the other 4 were regular jobs over the next 36 h.
- Got: task IDs grouped by day only.
- Workaround: Taskcluster `task/<id>/status` plus the Treeherder `jobs/?task_id=` API for each task.
