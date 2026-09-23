## Question: "a job where this test passed, on the config where it fails"

- Why: to compare job-level state (here, the port the harness's HTTP/3 server drew) between failing and passing jobs of the same config.
- Command: `fx-tests test netwerk/test/unit/test_http3_fast_fallback.js --task-ids --config macosx1500-aarch64-vms`
- Expected: a way to list some passing task IDs as well, e.g. `--passing-task-ids --limit 3`.
- Got: only failing task IDs. `--coverage` gives configs, not tasks.
- Workaround: took a macOS VM task from another test's failure list (`fx-tests test netwerk/test/unit/test_http3_proxy.js --task-ids`), then checked with `fx-tests task <id> --messages` that this test passed in it.
