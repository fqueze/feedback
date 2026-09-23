## The configs behind one failure mode

- Question: which configurations does issue 1 (the aria-expanded failure) hit, as opposed to issue 2 (timeouts)?
- Command: `fx-tests test browser/extensions/newtab/test/browser/browser_topsites_section.js --task-ids --issue 1 --limit 0`
- Expected: a per-config count for that issue (the per-config table filtered to the issue).
- Got: the per-config table is for all issues; the issue's task list is per day, so the breakdown needs a `grep | sed | sort | uniq -c` over the task list.
- Workaround: that pipeline over the saved output.
