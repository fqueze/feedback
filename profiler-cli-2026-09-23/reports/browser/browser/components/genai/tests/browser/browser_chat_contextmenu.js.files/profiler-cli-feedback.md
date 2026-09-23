## Question: "in which order did these markers happen, within the same millisecond?"

- Command: `profiler-cli thread markers --list --search ...` prints `t=1.652s` for UpdateDimensions, the WidgetPaintTask and the reflows around it; the order matters (reflow before or after UpdateDimensions).
- The list is chronological, so the order was there, but quoting it needed sub-ms times: I ran `marker info m-N --json` in a loop through python to print `start`/`end`. (Also: `marker info --json` has no `handle` field, so the loop had to carry it.)
- What could have shown it: a `--precise` / auto-precision time column when neighbouring rows share the rounded value.

## `--search "contextmenu - "` does not match a DOMEvent's displayed label

- Command: `profiler-cli profile markers --search "contextmenu - " --session ...`
- Expected: the `DOMEvent contextmenu - document` marker, as displayed.
- Got: `No markers match`. `--search contextmenu` does find it (via the eventType field). The displayed label is not searchable text.
- Workaround: search the bare event type and grep.

## (review) `--session` is rejected before the subcommand

- Command: `profiler-cli --session <id> zoom push 1.648,1.675`
- Expected: a global option, accepted anywhere, since every call of a shared-machine workflow needs it.
- Got: `error: unknown option '--session' (Did you mean --version?)`. It is only accepted after the subcommand.
- Workaround: moved it to the end. Cost: one call.
