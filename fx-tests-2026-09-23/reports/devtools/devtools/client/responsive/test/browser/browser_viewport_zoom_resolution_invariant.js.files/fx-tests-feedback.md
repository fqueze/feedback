## Question: did a try push run the configs that the removed skip-if covered?

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --test devtools/client/responsive/test/browser/browser_viewport_zoom_resolution_invariant.js --all-jobs --task-ids`
- Expected: some note that the removed skip-if (`os == 'win' && opt && standalone`) matches none of the configurations in the table, so "0 failures" says nothing about the skipped config.
- Got: a clean table of 7 configurations with all passing, and no mention that no `-standalone` job ran. I had to read the manifest diff and grep the job names myself. Same for `fx-tests try <rev> --profiles --task-ids`: the test does not appear at all, which reads as "passed", not "never ran where it was skipped".
- What would answer it: in `--test` mode, print the test's skip-if from central's manifest and flag configs matching it as "not run on this push".
