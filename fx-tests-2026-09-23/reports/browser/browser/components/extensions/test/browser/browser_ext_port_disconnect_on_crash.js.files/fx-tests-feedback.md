## Question: which of test A's failing jobs also have test B failing?
- Command: `fx-tests test <A> --task-ids --limit 0` and the same for B, then a regex over the text output to extract task IDs and `comm` them.
- Expected: a way to intersect, e.g. `fx-tests test <A> --task-ids --also-failing <B>`, or a plain one-ID-per-line output mode.
- Got: task IDs mixed into a human-readable listing (with `.0` run suffixes and `×2` markers), so a first naive regex also caught job names and extension UUIDs.
- Workaround: `rg -o '^\s+([A-Za-z0-9_-]{22})\.\d+\s' -r '$1'`.
