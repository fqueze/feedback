## Which document did each DOM change land in?

Question: in a content process hosting two about:newtab/about:home documents, which of them did a given `SetNeedStyleFlush` (or DOMEvent) touch?

- Command: `profiler-cli thread markers --search SetNeedStyleFlush,ReceiveMessage,DocumentLoad --list --limit 0 --session <s>`
- Expected: each row to say which page (innerWindowID and, better, its URL from the profile's page list) the marker belongs to, so interleaving with ReceiveMessage rows shows which document reacted to which message.
- Got: rows with no window/page information; `innerWindowID` only appears in `marker info` as a raw payload field, and nothing maps it to a URL.
- Workaround: `--list --json` piped to a Python script grouping consecutive markers by `data.innerWindowID`, and matching the id against the `DocumentLoad` marker ("Document about:home loaded after ...") to find which id is the test's page. `--group-by field:innerWindowID` gives counts per id but loses the chronology against the messages.
