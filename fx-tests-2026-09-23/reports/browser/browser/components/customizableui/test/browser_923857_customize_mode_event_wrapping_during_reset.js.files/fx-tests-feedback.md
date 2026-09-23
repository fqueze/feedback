# fx-tests feedback

## Question: "where can I get a per-test profile of this failure, when my config never uploads one?"

- Commands: `fx-tests test browser/components/customizableui/test/browser_923857_customize_mode_event_wrapping_during_reset.js --profiles --day 2026-09-21`, then `fx-tests task La7nMgVcTRqcRoXanURxtg --profiles`
- Expected: some hint that the test's only failing config (linux2404-64-tsan/opt) never has per-test profiles, and that the same message (`sess is null`) fails a neighbour test in the same job (`browser_878452_drag_to_panel.js`) on 25 other configs, where per-test profiles exist.
- Got: only resource-usage profiles, and "No failing test named a per-test profile in this job." on every TSAN job. I found the other configs only by running `fx-tests test` on each neighbour by hand.
- Workaround: `fx-tests test <neighbour> --task-ids --day <d>`, pick a non-TSAN task, `fx-tests task <id> --profiles`. Its per-test profile covered the whole manifest's browser session and showed the leaking mousedown.
- What could have shown it: `task --profiles`, when no per-test profile exists, could list other tests in the job that fail with the same message, with the configs where they do have per-test profiles.
