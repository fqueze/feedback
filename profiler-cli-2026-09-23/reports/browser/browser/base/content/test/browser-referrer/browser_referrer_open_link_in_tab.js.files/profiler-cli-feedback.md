## Question: "which markers of kind B fall inside each instance of marker A?"

- Command: `profiler-cli thread markers --list --limit 0 --search "name:RefreshDriverTick,name:BrowserTestUtils,name:Reflow"`, then a Python script over the saved output.
- Expected: a way to ask, for each `BrowserTestUtils openNewForegroundTab` interval, how many `RefreshDriverTick` / `Reflow` markers started inside it, e.g. `thread markers --search RefreshDriverTick --group-by-enclosing "openNewForegroundTab"`, or `--during-marker` (which `filter push` has for samples) on `thread markers`.
- Got: a flat list; I parsed its `t=` and duration columns myself (and the durations mix `ms`, `μs`, `ns` and negative values like `-230,625ns`, which makes that parsing fragile).
- Workaround: script over the text output.

## Question: "which processes went PREALLOC -> FOREGROUND?" (review, 2026-09-22)

- Command: `profiler-cli thread markers --search "PREALLOC -> FOREGROUND" --list --limit 0` (also `profile markers --search "PREALLOC -> FOREGROUND"`, and `--search "-> FOREGROUND"`).
- Expected: the `Process Priority` markers whose printed label is `priority: PREALLOC -> FOREGROUND`.
- Got: 0 markers. The label is built from the `Before`/`After` fields, so the printed text is not searchable, and a term starting with `-` is silently taken as an exclusion. `--search "PREALLOC "` matched.
- Workaround: search `PREALLOC` and read the list. Searching the rendered label too, or warning when a term parses as an exclusion with no `field:`, would have avoided it.
