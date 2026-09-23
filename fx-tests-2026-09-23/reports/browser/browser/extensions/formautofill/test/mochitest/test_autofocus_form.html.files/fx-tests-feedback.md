## `fx-tests test <path> --bugs` prints nothing when no open bug names the test

- Command: `fx-tests test browser/extensions/formautofill/test/mochitest/test_autofocus_form.html --bugs`
- Expected: a Bugs section, even if it says "none open". Ideally also the closed tracking bug: 1788890 "Intermittent .../test_autofocus_form.html | single tracking bug", RESOLVED INCOMPLETE on 2026-06-22.
- Got: the same output as without `--bugs`, with no Bugs section, so "no bug" cannot be told apart from "flag ignored".
- Workaround: `curl https://bugzilla.mozilla.org/rest/bug?summary=test_autofocus_form`.

## Jobs killed at max run time are missing from the test's counts

- Command: `fx-tests test <path>` (and `--task-ids`).
- Expected: the macosx1500-aarch64-vms jobs of 2026-09-17 … 09-21, where this test timed out twice per job before the job hit max run time (for example AWpB…, whose log is in the test_autofill_and_ordinal_forms report's files).
- Got: no macOS failure at all. The verdict lists only 3 Linux configs.
- Workaround: the sibling report's annotated-job list on bug 1803644, and grepping the job logs.
