## review-browser_originattrs_reopenin.js: `--search` on the label text as printed matches nothing

- Command: `profiler-cli thread markers --search "popuphiding - menupopup" --list --session <s>` (also `--search "command - ,mouseover - ,..."` mixed into a longer OR list).
- Expected: the DOMEvent markers whose list row reads `popuphiding - menupopup@... id="tabContextMenu"`.
- Got: `0 markers`, silently; in the OR list those terms just dropped out, so the `command - menuitem` and `mouseover` rows were missing from a list I took as complete.
- Workaround: search the event type alone (`--search popuphiding`, `--search command`) and filter by eye.
- Could have: matched against the rendered label too, or warned that a term containing ` - ` matched no field.
