## Question: "in which jobs of a marionette config does this message appear, and since when?"

- Command: none possible. `fx-tests failures --message getProfileByName` and `fx-tests test <marionette test>` have no marionette data.
- Expected: failing jobs of `test-linux2404-64/opt-marionette-integration-headless` grouped by message, with task IDs and revisions, like `failures --tests` / `test --task-ids` give for mochitest.
- Got: nothing, so I had to script it: `treeherder-cli --similar-history <job> --similar-count 200 --json`, then Treeherder `/api/jobs/?id__in=` for task IDs, then download 200 `marionette_errorsummary.log` files and group by message.
- Why it mattered: the caller's premise ("getProfileByName in ~38 of 88 headless jobs") came from the job's overall failure rate. Grouping by message showed the mode in 1 job of 200. A per-message view would have caught that at once.
