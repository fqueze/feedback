## Question: "did this test run on the try push that removed its skip-if, and did it pass?"

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --profiles --task-ids`
- Expected: some line saying the test was not among the failures.
- Got: 722 lines about other tests, and nothing about this one. That fits both "passed" and "never ran".
- Workaround: `fx-tests try <rev> --test <path> --all-jobs`, which answered it (21/21 passed on 7 configs) but reads every job.
