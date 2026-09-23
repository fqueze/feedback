## Question: what is the stack of this uncaught-rejection failure?

- Command: `fx-tests test browser/components/contextualidentity/test/browser/browser_eme.js --task-ids --limit 0 --full-messages`, and `fx-tests task St8MB7UrR7eWhCsZeb-Erw --profiles`
- Expected: for a message as uninformative as `uncaught rejection: ` (empty reason), the rejection stack the harness logs on the following lines (`reportEMEDecryptionProbe@resource:///actors/EncryptedMediaParent.sys.mjs:292:35 ...`). The per-test profile's TestStatus marker does not carry it either.
- Got: only `uncaught rejection: `; all 26 failures look identical and say nothing about where they come from.
- Workaround: downloaded `public/logs/live_backing.log` and grepped after the TEST-FAIL line.

## `--bugs` prints nothing when no bug is found

- Command: `fx-tests test browser/components/contextualidentity/test/browser/browser_eme.js --bugs`
- Expected: a line saying no bug names this test (or listing closed ones such as bug 1877511, the resolved-INCOMPLETE tracking bug).
- Got: output identical to the command without `--bugs`; `--json` has `annotatedBugs: []`. Not obvious whether the flag did anything.
