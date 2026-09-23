## Which caller of a test helper failed: `marker stack` has no JS line numbers

- Question: the failure `[test_SelectableProfileLifecycle : 48] Should not have set a badge image` is inside a helper (`assertNotBadged`) called from three places in the test. Which call failed?
- Command: `profiler-cli marker stack m-23 --session browser-lifecycle-1`
- Expected: JS frames with their line (e.g. `test_selectable_profiles_lifecycle.js!test_SelectableProfileLifecycle:278`), if the profile's frame table has line numbers.
- Got: function names only (`...!assertNotBadged`, `...!test_SelectableProfileLifecycle`).
- Workaround: inferred the call site from the order of the neighbouring TEST-PASS markers (the failure followed the line-272 pass).

## A misspelt exclusion in `thread markers --search` silently excludes nothing

- Command: `profiler-cli thread markers --session browser-lifecycle-1 --search "-name:TaskController::AddTask,-field:name:DummyEvent" --list --limit 0` (my typo: `-field:name:` instead of `-name:`)
- Expected: an error or warning that no marker has a payload key `field`.
- Got: the first exclusion applied, the second silently matched nothing, and 30 000 `DummyEvent` Runnables still flooded the list.
- Workaround: `| grep -v DummyEvent`.

## An empty sample range prints an empty call tree, without saying there are no samples (review)

- Question: what JS ran inside a 6.5 ms `setTimeout() for maybeSetupDataStore/<` Runnable?
- Command: `profiler-cli zoom push m-118 --session browser-review-test_selectable_profiles_lifecycle.js-1` then `profiler-cli thread samples-top-down --include-idle --session ...-1`
- Expected: "0 samples in this range" (CI samples are sparse, so this is common for short markers).
- Got: a header and `Top-Down Call Tree:` with nothing under it, which reads like a filter or selection problem.
- Workaround: none needed once understood; concluded no sample fell inside the runnable.
