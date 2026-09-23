## `errors --message` with a raw number matches nothing

- Command: `fx-tests errors --harness xpcshell --message "Error:-2147009284" --day 2026-09-10`
- Expected: the `Failed to launch tab subprocess @CreateProcess (Error:-2147009284)` warnings, which the job's resource-usage profile has.
- Got: `No markers matched.`, with no hint why.
- Workaround: `--message "Failed to launch tab subprocess"` matches. The stored message is normalized to `(Error:-<num>)`, so a literal number can never match. The command could normalize the query the same way, or say "messages are normalized; numbers become <num>" when nothing matches.
