## review-browser_rules_highlight-property.js (2026-09-22)

**Question: for every occurrence of marker A (content `inspector:getWalker()`), where does the nearest marker B (`resize - window` DOMEvent) on the same thread fall relative to it, and how long did B take?**

- Command: `profiler-cli profile markers --search "eventType:resize" --limit 0 --json` and `profiler-cli profile markers --search "inspector:getWalker" --limit 0 --json`, then a Python script joining the two by thread and time.
- Expected: some way to line up two marker kinds per occurrence, across the content threads that alternate between tests. For example `profile markers --search A --relative-to B`, giving one row per A with the signed offset and duration of the nearest B on the same thread.
- Got: two flat lists (135 resize and 46 getWalker rows across about 10 threads) that I had to join by hand.
- Workaround: the script. This is the question behind every "does step X always come before step Y in the passing iterations" check.
