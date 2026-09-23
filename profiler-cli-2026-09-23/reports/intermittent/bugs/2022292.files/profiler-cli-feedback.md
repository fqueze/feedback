## A field search cannot go into a link

- Question: "did any focus, activate or deactivate DOM event reach the parent main thread in this session".
- Command: `profiler-cli thread markers --search "eventType:focus,eventType:activate,eventType:deactivate" --list --limit 0`. This answers it exactly: only `mochitest-load` matched.
- The link can only carry a bare search. `profile-link.py --search focus` opens the marker table on 60 unrelated markers, mostly `Preference Read` of `accessibility.tabfocus` and similar. To show "no focus event" in a link, I had to link the whole `DOMEvent` list (1006 markers) and let the reader scan it.
- What would help: `profile-link.py` (or `profiler-cli`) could say when a field search has no URL equivalent, and suggest the closest bare search.
