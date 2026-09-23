## Question: which document does a DOMEvent / "requestAnimationFrame callbacks" marker belong to?

- Command: `profiler-cli marker info m-465` (DOMEvent `focus - document`) and `thread markers --search "name:requestAnimationFrame callbacks" --list`
- Expected: the page URL of the marker's `innerWindowID` (the profile's `pages` table has it), at least in `marker info`, ideally in the list row for DOMEvent/rAF markers whose target is just `document`/`window`.
- Got: `innerWindowID: 103` under "Other payload fields (no schema)", with no way to resolve it: no command lists the pages table, and `--search innerWindowID:103` matches nothing.
- Workaround: `marker info --json` per marker for the ids, then downloaded the profile separately and read `pages` with python to learn 103 = about:addons, 94 = browser.xhtml.

## Question: which JS function was on the stack for each of these markers?

- Command: `thread markers --search name:SetNeedStyleFlush --list` over a 190 ms range (103 markers, all `✓`).
- Expected: an option to show the top JS frame (or first N frames) in the list rows, since the list is otherwise 103 identical "SetNeedStyleFlush instant ✓" lines.
- Got: no stack info in the list; `marker stack` takes a single handle.
- Workaround: shell loop of `marker stack m-N --json` over 100 handles plus a python script to print the first JS frames.
