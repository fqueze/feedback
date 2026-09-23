## `task --messages` lists TEST-KNOWN-FAIL (todo_is) lines as failure messages

- Command: `fx-tests task fS1K7IYQQhGQPReYx_wD9g --messages`
- Expected: only the TEST-UNEXPECTED-* messages of the failing test (2 here: "Initial resolution is as expected" and "Replaced meta viewport content "width=800, initial-scale=1" resolution is as expected").
- Got: 5 messages, 3 of which ("... resolution matches Chrome resolution.") are `todo_is` TEST-KNOWN-FAIL lines that fire on every run, passing or not. The default view also says "(+4 more messages for this test)".
- Workaround: read the test log in the profile to see which were TEST-KNOWN-FAIL.

## `try <rev>` gives no way to tell that the removed skip-if's config never ran

- Question: "did this try push exercise the config the removed `skip-if` covered?"
- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --test devtools/client/responsive/test/browser/browser_viewport_changed_meta.js --all-jobs`
- Got: 7 configs, 21/21 passed, and nothing saying that the removed condition (`os == 'win' && opt && standalone`) matches none of them. The plain `try <rev> --profiles --task-ids` output does not mention the test at all, which reads the same as "passed everywhere".
- Would have helped: under `--test`, the test's skip-if conditions on central, and which of them no job of the push matched ("no standalone job in this push").
- Workaround: `git show` the manifest change, compare by hand; then `fx-tests test <path>` to find the central failure on the neighbouring debug-standalone config.
