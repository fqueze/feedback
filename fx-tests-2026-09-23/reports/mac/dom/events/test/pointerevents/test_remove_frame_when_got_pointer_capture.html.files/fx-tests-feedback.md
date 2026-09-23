## Question: "in the jobs where this test failed, which other tests failed too, and how often?"

- Command: `fx-tests test dom/events/test/pointerevents/test_remove_frame_when_got_pointer_capture.html --task-ids --limit 0`, then the same for `test_synthesized_touch.html`, then `rg` + `comm` over the two task ID lists.
- Expected: a way to see co-failures, e.g. `fx-tests test <path> --co-failures`, listing the other tests that failed in the same jobs with counts ("test_synthesized_touch.html failed in 323/323 of these jobs; it also failed in 18 more"). For an order-dependency or leak diagnosis, that is the first question after the rates.
- Got: two separate task lists that had to be intersected by hand.
- Workaround: a shell script over the text output.

## `--bugs` prints nothing when no bug names the test

- Command: `fx-tests test dom/events/test/pointerevents/test_remove_frame_when_got_pointer_capture.html --bugs`
- Expected: a "Bugs" section, even one saying "none found". Here, a closed tracking bug (1791387, RESOLVED INCOMPLETE) and a FIXED 2018 bug name the test in their summaries.
- Got: exactly the same output as without `--bugs`. `--json` has `annotatedBugs: []` and no key for bugs found by name.
- Workaround: `curl 'https://bugzilla.mozilla.org/rest/bug?summary=<test file name>'`.
