## Question: the response status and URL of a Network marker, from `marker info --json`

- Command: `PROFILER_CLI_SESSION_OWNER=review-browser_net_copy_headers.js profiler-cli marker info m-44 m-45 --json --session review-browser_net_copy_headers.js-3`
- Expected: payload in `fields[]`, as the guide says ("field" is the payload key from `marker info --json` fields[].key).
- Got: `fields` is empty for Network markers ("raw payload, no schema"); the keys (`responseStatus`, `URI`, `redirectType`) are only in `rawFields`. `--search responseStatus:401` still works on them.
- Workaround: text `marker info`, which prints the raw payload.
- Could have shown: the guide mentioning `rawFields` for schema-less markers, or `fields` populated from the raw payload.
