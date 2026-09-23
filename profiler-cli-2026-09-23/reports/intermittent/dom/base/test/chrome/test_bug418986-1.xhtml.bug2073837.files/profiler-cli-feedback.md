## `--group-by field:eventType` truncates groups, and the absence question needs a second call

- Command: `profiler-cli thread markers --session <s> --search DOMEvent --group-by field:eventType`
- Question: "were any focus / blur / activate events dispatched in this session?"
- Got: the top ~30 groups only; `focus` not being listed could have meant truncated. Needed `--limit 0` and a grep over the output.
- Expected: a truncation line (`… N more groups`), or a way to ask for specific values, e.g. `--search eventType:focus,eventType:activate` printing an explicit "0 markers" per requested value.
