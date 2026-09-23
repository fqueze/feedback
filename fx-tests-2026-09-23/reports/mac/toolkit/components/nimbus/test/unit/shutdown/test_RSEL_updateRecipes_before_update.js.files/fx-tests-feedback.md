## `test --bugs` says nothing when no bug names the test

- Command: `fx-tests test toolkit/components/nimbus/test/unit/shutdown/test_RSEL_updateRecipes_before_update.js --bugs`
- Expected: a Bugs section, or a line such as "no bug names this test".
- Got: output identical to the run without `--bugs`, so I could not tell whether the lookup ran and found nothing, or did not run.
- Workaround: Bugzilla REST quicksearch on the file name, which returned no bugs.

## `failures --message` does not search crash signatures, and does not say so

- Command: `fx-tests failures --harness xpcshell --message "child process hang at shutdown"`
- Expected: the tests behind that message, since `fx-tests test <path>` lists it under Issues as `CRASH child process hang at shutdown`.
- Got: "No failure matched. ... Check --path, --message and --component for typos."
- Workaround: `fx-tests crashes --harness xpcshell` (19,591 crashes in 507 tests). The "No failure matched" message could say that crash signatures are under `crashes`.
