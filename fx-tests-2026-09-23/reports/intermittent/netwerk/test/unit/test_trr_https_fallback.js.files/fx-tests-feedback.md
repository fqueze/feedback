## Question: "which failure modes does this test really have?"

- Command: `fx-tests test netwerk/test/unit/test_trr_https_fallback.js` then `fx-tests test ... --task-ids --limit 0`
- Expected: the Issues list to separate failures that share a first message but differ in the next one (here `Could not get contentLength` followed by `Failed to load URL: 805a2fe7` on macOS vs `804b004c` on Linux, which are two different subtests and mechanisms).
- Got: one row "6x Could not get contentLength"; the discriminating second message only shows with `fx-tests task <id> --messages`, one job at a time. Also, `--task-ids --limit 0` printed three of the five jobs twice (once under their date, then again after the list).
- Workaround: `fx-tests task <id> --messages | grep -A12 <test>` on each job.
