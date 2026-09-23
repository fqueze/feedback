## Question: did any `focus` DOM event fire in this session?

- Command: `profiler-cli thread markers --search "eventType:focus" --list --limit 0 --session browser-test_login_item.html-1`
- Expected: `focus` events only, or none.
- Got: 8 `framefocusrequested` markers, because `field:value` is a substring match. The answer (no `focus` event at all) only came from running `--group-by field:eventType` next to it.
- What the output could have shown: a way to ask for an exact field value (for example `eventType:=focus`), or `--help` saying that `field:value` is a substring match.
- Workaround: `thread markers --search DOMEvent --group-by field:eventType`, then read the group names.

## Question: which threads does this profile have? (review of this report)

- Command: `profiler-cli --session browser-review-test_login_item.html-1 profile info`
- Expected: `--session` accepted before the subcommand, like a global option.
- Got: `error: unknown option '--session' (Did you mean --version?)`.
- What the output could have shown: accept `--session` in either position, or say "put --session after the subcommand".
- Workaround: `profiler-cli profile info --session <id>`.
