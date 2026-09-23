## Question: which repository (mozilla-central vs autoland) are the failures from?

- Command: `fx-tests test browser/components/contextualidentity/test/browser/browser_switchTab_across_user_context.js --task-ids --limit 0`
- Expected: a repo column or a per-repo split, since a config like `linux2404-64-shippable/opt-...-no-nv` runs on both m-c and autoland and here fails only on m-c (the build's update channel differs: `nightly` vs `nightly-autoland`).
- Got: task IDs grouped by day only. I had to run `fx-tests task <id>` on each task to see its repo, and query Treeherder to learn autoland shippable runs pass.
- What would have answered it: a `repo` column in the Failing configurations table (fails/runs per repo), or in `--task-ids`.

## Question: a passing run of this test on config X, to compare with the failure

- Needed: a task ID for a passing autoland `linux2404-64-shippable/opt-mochitest-browser-chrome-no-nv` job that ran `browser/components/contextualidentity/test/browser/browser.toml`.
- Workaround: Treeherder `/api/project/autoland/jobs/?job_group_symbol=M-no-nv&platform=linux2404-64-shippable` then `/api/project/autoland/push/group_results/?revision=<rev>` to find which task ran the manifest, then the Taskcluster queue to name the tasks.
- What would have answered it: `fx-tests test <path> --coverage --task-ids` listing a few recent passing task IDs per config (and repo).
