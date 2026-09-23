## `thread markers --search` does not match the DOMEvent text the list prints

- Command: `profiler-cli thread markers --session <s> --search 'click - ' --list --limit 0` (review of this report)
- Expected: the `DOMEvent` rows the same list prints as `click - image@… id="star-button"`.
- Got: `No markers match the specified filters.` The printed label is built from `eventType` and `target`, and is not itself searchable.
- Workaround: `--search 'eventType:click'`. Searching the printed text, or saying in the "no match" message which fields were searched, would have saved a call.
