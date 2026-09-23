## Question: "the mouse DOMEvents on this thread" (review-browser_918049)

- Command: `profiler-cli thread markers --session browser-review-918049-3 --search "DOMEvent" --search "mouse" --list --limit 0`
- Expected: both terms applied (AND), or an error saying `--search` can only be given once.
- Got: only the last `--search` was used, silently. The 2840 results included `Preference Read` markers that matched "mouse".
- Workaround: `--search name:DOMEvent` inside a `zoom push`, then grep for mousedown/mouseup/dragstart/dragend.
