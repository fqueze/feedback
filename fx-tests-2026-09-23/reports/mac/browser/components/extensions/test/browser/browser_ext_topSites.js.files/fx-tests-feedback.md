## `--bugs` prints nothing when no bug names the test

- Command: `fx-tests test browser/components/extensions/test/browser/browser_ext_topSites.js --bugs`
- Expected: a line such as "Annotated bugs: none" so "no bug" is distinguishable from "the flag did nothing".
- Got: the same output as without `--bugs`; only `--json` shows `"annotatedBugs": []`.
- Workaround: `--bugs --json` and read `annotatedBugs`.

## Question: "what did my test do in this job, and where is its profile?"

- Command: `fx-tests task YpSGykRmTAimSGoj0tse0g --profiles | grep topSites`
- Expected: a way to ask for one test's entry in a job (e.g. `--test <path>`), since the job had 53 failures from a cascade.
- Got: nothing: the FAILED list is cut at 20 entries and my test was entry 40-something.
- Workaround: `--limit 0` and grep.

