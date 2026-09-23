## Review of report 2071976 (review-2071976)

- Question: "which marker does this link's `marker=17269` point at?"
  Command: `profiler-cli load --session review-2071976-3 '<link with marker=17269>'`, then `thread markers --search ... --list`, then `marker info m-N --json` on each candidate.
  Expected: `load` of a link carrying `marker=N` (or a `marker info --index N`) to name the handle of marker N.
  Got: `load` reports only thread and range. The list shows handles (`m-41`), never indexes, so I had to loop `marker info --json` over 20 candidates until one reported `markerIndex` 17269.
  Workaround: shell loop over `marker info <m> --json | rg markerIndex`.
