## Reflow marker stack is its cause stack, but is printed as if captured at the marker

- Command: `profiler-cli marker info m-889 --session browser_viewport_zoom_resolution_invariant.js-1` (thread t-17)
- Expected: either the stack at the time of the reflow (4.260s), or a label saying this is the stack where the reflow was requested.
- Got: `Time: 4.260s - 4.260s` then `Stack trace: Captured at: 4.182s ... PBrowser::Msg_Deactivate`. Nothing says it is the cause stack, so it reads as a wrong or mismatched stack; I spent a few queries checking whether the handle had been remapped after a zoom change.
- Workaround: knowing that Layout markers carry the cause stack. Labelling it "Cause stack (requested at 4.182s)" would avoid the doubt.

## Question: "is this handle the marker a profiler link's `marker=N` points to?" (review)

- Command: `profiler-cli marker info m-382 m-369 m-385 m-383 --session review-browser_viewport_zoom_resolution_invariant.js-1`
- Expected: each record's marker index, which is what a link's `marker=N` holds, printed next to its name and time.
- Got: name, type, time, thread, fields, stack. No index. Checking 17 links meant `--json` plus a Python filter for `markerIndex` on each batch.
- Workaround: `--json | python3 -c ...` to print `markerIndex`, `name`, `start`. An `Index: N` line in the text output would answer it.
