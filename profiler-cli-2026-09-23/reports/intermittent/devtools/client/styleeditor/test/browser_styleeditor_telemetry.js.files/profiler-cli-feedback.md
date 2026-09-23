## Marker times lose sub-second precision past one minute

- Command: `profiler-cli thread markers --search "ruleview-rule-source theme-link" --list --limit 0 --session <s>` and `profiler-cli marker info m-6 m-7 --session <s>`, on a 1m26s profile.
- Expected: times like `t=82.314s`, precise enough to order markers and cite them.
- Got: `t=1m22s` for both markers, and `Time: 1m22s - 1m22s (171.22ms)` in `marker info`. The click and the failure it caused cannot be told apart, or ordered, from the text output.
- Workaround: none needed here, since the order was known from the other profile. Otherwise `--json`.

## `--session` is rejected before the subcommand (review)

- Command: `profiler-cli --session <s> thread markers --category Test --search X --list --limit 0`
- Expected: the session option to be accepted anywhere, as a global option, like `load ... --session <s>`.
- Got: `error: unknown option '--session' (Did you mean --version?)`.
- Workaround: put `--session <s>` after the subcommand's own arguments.
