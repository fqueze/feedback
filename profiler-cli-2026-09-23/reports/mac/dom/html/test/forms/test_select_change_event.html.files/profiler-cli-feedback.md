## Question: which of these event types (mozshowdropdown, change, input) fired on the content thread

- Command: `profiler-cli thread markers --search "eventType:mozshowdropdown,eventType:change,eventType:input" --list --limit 20 --session tsce-1`
- Expected: only DOMEvent markers whose event type is one of those three.
- Got: 55,923 markers, starting with every `readystatechange` (field:value is a substring match, so `eventType:change` matches `readystatechange`, `slotchange`, `visibilitychange`...). There is no exact-match form for a field value.
- Workaround: `--search 'id="select"' --group-by field:eventType`, which answered it (10 keydown, 10 keyup, 1 mozshowdropdown, no change/input). An exact-match operator (e.g. `eventType:=change`) would have answered it directly.
