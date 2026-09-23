## Question: is there a bug for this failure message?

- Command: `fx-tests test browser/components/mozcachedohttp/test/unit/test_host_integration.js --bugs`
- Expected: some result for the Bugs section, even "none annotated".
- Got: the normal output with no bugs section at all (in the JSON, `annotatedBugs: []`). The harness retry passes, so this test's crashes are never starred. But bug 1994039 exists for exactly this assertion, and a Bugzilla quicksearch for the assertion text found it.
- What would have answered it: an explicit "no annotated bugs" line, plus a search by the top failure or assertion message (`###!!! ASSERTION: Network cache reported memory consumption is not at 0`) when no bug names the test.

## `fx-tests crash` fails on a `[Unknown]` crash

- Command: `fx-tests crash W4MtUkTiQaKYQfCGMPP3Tg.0 a369b652-0b21-4494-8f26-e5fe43d11a75` (task and minidump IDs printed by `fx-tests test … --task-ids --issue 2`)
- Expected: the crash details, or exit 4 if the artifact is gone.
- Got: `response is not valid JSON: Unexpected end of JSON input … This looks transient`. The JSON artifact is probably missing or empty for an unsymbolicated crash, which is not transient.
- Workaround: the job's resource-usage profile shows the assertion text for that run.
