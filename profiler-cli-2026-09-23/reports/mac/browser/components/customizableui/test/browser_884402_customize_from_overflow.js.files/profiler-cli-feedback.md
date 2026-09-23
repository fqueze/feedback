# profiler-cli feedback (browser_884402_customize_from_overflow.js)

## Question: "from when to when did a refresh tick reason stay present?"
- Command: `thread markers --search RefreshDriverTick --list --limit 0` (991 markers over 23 s).
- Expected: a way to see how a marker's label changes over time, e.g. runs of identical labels ("t=16.851–23.518s, 401× `Tick reasons: HasObservers (Synthetic mouse move event [Display])`").
- Got: one row per marker, too long to read; I had to `--json` and collapse consecutive identical labels in a script.
- What would have answered it: a `--runs` / "collapse consecutive identical labels" option on `--list`, or `--group-by label` with first/last timestamps per group.

## `--group-by field:<key>` with a key that does not exist
- Command: `thread markers --search RefreshDriverTick --group-by field:reasons`
- Expected: an error naming the valid field keys for the matched markers (here the key is `name`, printed as "Details").
- Got: `(no value): 390 markers`, which reads as "the field is empty", not "no such field".
- Workaround: `marker info` to find the key, then `--json`.
