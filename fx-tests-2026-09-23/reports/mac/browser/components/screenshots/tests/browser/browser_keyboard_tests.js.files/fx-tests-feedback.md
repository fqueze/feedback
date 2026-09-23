## Question: which manifest did each failure run in? (test listed in two manifests, browser.toml and browserSidebarRevamp.toml with dupe-manifest)

- Command: `fx-tests task <taskId> --profiles` and `fx-tests test <path> --task-ids`
- Expected: the manifest (or at least the pref variant) next to each failing execution, since the same test file runs under different manifest prefs (`sidebar.revamp=true/false`) and at a different position in each.
- Got: only the test path; I had to load each job's resource-usage profile in profiler-cli and look at the manifest-level `test` markers.
- Workaround: `profiler-cli thread markers --search "name:test" --list --limit 0` on `profile_resource-usage.json` for 5 jobs.
