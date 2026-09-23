# profiler-cli feedback (browser_chat_page.js.label)

## Question: across every opening of one popup, which came first — the translation or popupshown — and where in the refresh interval did each opening start?

- Command: `profiler-cli thread markers --session <s> --list --limit 0 --search "tabContextMenu,L10nMutationsFinished,name:RefreshDriverTick" --json`, then `race.py` (in this directory) pairing each `popupshowing` with the next `popupshown`, the next `L10nMutationsFinished` and the previous/next `RefreshDriverTick`.
- Expected: a way to read, per occurrence of marker A, the delay to the next marker B and C (and the previous D) without a script.
- Got: the flat list answers it for one occurrence by eye; for 9 openings per profile over 3 profiles it took a script.
- What the output could have shown: something like `thread markers --relative-to <search>` printing each match's offset to the nearest preceding/following markers of other searches.
