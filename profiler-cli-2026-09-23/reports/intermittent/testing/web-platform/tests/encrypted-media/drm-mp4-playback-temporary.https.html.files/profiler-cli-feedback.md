## "Did the license request go out during test X, and how busy was the machine after it?" (resource-usage profile)

- Command: `profiler-cli thread markers --search name:NetIO --list --limit 0 --json` (and `name:CPU Use`, `name:test`), then a Python script listing each NetIO interval's Sent/Received and CPU relative to one `test` marker's start, and summing them over the rest of the test.
- Expected: `zoom push m-<test marker>` followed by a marker listing that shows the payload fields per row (Sent/Received/CPU%) compactly, or sums them over the zoom.
- Got: with a zoom, `thread markers` gives counts and duration stats per marker name, not the payload values or sums; `--list` rows show no NetIO byte counts.
- Also: in `--json`, `CPU Use`'s `cpuPercent` is a string (`"9.5%"`), NetIO's byte fields are numbers.
