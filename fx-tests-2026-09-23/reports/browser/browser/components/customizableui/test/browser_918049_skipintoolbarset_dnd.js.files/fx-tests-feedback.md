## Question: "do these two tests fail in the same jobs?"

- Command: `fx-tests test <A> --task-ids --limit 0` and `fx-tests test <B> --task-ids --limit 0 --issue 2`, then `rg`/`awk`/`sort`/`comm` over the text to compare task-ID sets.
- Expected: a way to ask which other tests fail in the same jobs as this one (co-failures), e.g. `fx-tests test <path> --co-failures`, listing each other test with "fails in N of this test's M failing jobs".
- Got: per-test task lists only; the overlap needed a script.
- Why it matters: for order-dependent victims, "the 493 jobs where X fails are exactly the 493 where Y fails in mode 2" is the key fact, and `fx-tests task` shows it for one job only.

## `fx-tests test <path> --bugs` is silent when no bug names the test

- Command: `fx-tests test browser/components/customizableui/test/browser_918049_skipintoolbarset_dnd.js --bugs`
- Expected: a line such as "Bugs naming this test: none".
- Got: the normal summary with no Bugs section at all, which looks the same as the flag being ignored. (For browser_878452 the section does appear.)
- Workaround: `fx-tests intermittent --test <path>`, whose exit message does say no bug matched.
