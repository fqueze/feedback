## `--bugs` prints nothing when no bug names the test

- Command: `fx-tests test dom/base/test/test_bug333198.html --bugs`
- Expected: a Bugs section, saying "none" when no sheriff-annotated bug names the test.
- Got: the normal output with no Bugs section at all, so "no bug" and "the lookup did not run or failed silently" look the same.
- Workaround: Bugzilla REST `quicksearch=test_bug333198` (returned no bug).

## Question: "which manifest ran just before this test's manifest, in every run, passing or failing"

- An order-dependency across a browser restart (the preceding manifest leaves the OS in a bad state) needs the predecessor manifest for passing runs too, to build a fail/pass table per predecessor.
- `fx-tests test <path> --task-ids` gives only failing tasks. I had to pull every completed job of the configs from the Treeherder jobs API (about 4,000), fetch each task's `public/test_info/manifests.list`, and join with the failing task IDs by script.
- What would have answered it: the task IDs of passing runs (`--task-ids --include-passing`), or better, the manifest that ran before the test's manifest in each run, grouped by pass/fail (here: 40/80 fail after `dom/base/test/fullscreen/mochitest.toml`, 0/901 after anything else).
