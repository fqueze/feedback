# fx-tests feedback (test_linearEasing.js)

## Question: "which other tests share this failure message?" when the message is a crash signature

- Command: `fx-tests failures --harness xpcshell --message "child process hang at shutdown" --tests --limit 0`
- Expected: the tests failing with that message (it is the #1 issue under `fx-tests test`'s
  Issues, shown as `CRASH child process hang at shutdown`).
- Got: "No failure matched. Searched 4,986 tests ..." with no hint that crashes are elsewhere.
- Workaround: `fx-tests crashes --harness xpcshell` (19,591 crashes in 507 tests).
- Suggestion: when `failures --message` matches nothing but the text is a crash signature, say
  "this is a crash signature; see `fx-tests crashes --signature ...`".

## Question: "which child process type hung?" for `child process hang at shutdown`

- Command: `fx-tests crash <task> <dump>` 
- Got: the stacks, which were very useful; the process type only via the bottom frame
  (`Firefox Nightly GPU Helper + 0x6b5`) in `--frames 0` output.
- Suggestion: print the process type (annotation / executable name) in the header next to
  "This looks like a HANG".
