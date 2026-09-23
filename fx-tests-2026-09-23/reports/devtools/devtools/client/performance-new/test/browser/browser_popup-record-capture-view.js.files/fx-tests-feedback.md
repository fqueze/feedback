## Did the configs of the removed skip-if run on the try push?

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --test devtools/client/performance-new/test/browser/browser_popup-record-capture-view.js --all-jobs --task-ids`
- Expected: since the question for a try push that removes a `skip-if` is whether the skipped configs now pass, some note that the removed condition (`... && tsan`) matched no job on the push.
- Got: a table of 7 configs, 21/21 passed, with nothing saying that no TSAN job ran at all. Without checking, that reads as "the skip-if can go".
- Workaround: listed the push's jobs from the Treeherder API (`/api/jobs/?push_id=...`) with a script, and looked for `tsan` in the job names: there were none.
- What would have answered it: a line under the `--test` table naming the test's `skip-if` conditions on central (fx-tests already knows them, `Issues` lists `SKIP os == 'linux' ... && tsan` 228x) and whether any job on the push matched them.
