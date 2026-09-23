## Which tests were running at time T, and when did each start and end?

- Command: `profiler-cli thread markers --session <s> --search "name:test" --list --limit 0` inside a `zoom push 520,532`
- Expected: start and end precise enough to order tests a few hundred ms apart.
- Got: `t=8m47s` with a duration of `2.161s`. Several tests start inside the same second, so their order cannot be read. With `--json`, `end` is `null` for these interval markers, so the end has to be computed as `start + duration`.
- Workaround: `--json | jq '.flatMarkers[] | [.start, .start+.duration, .data.status, .data.test]'`.

## Which process slot did each test launch in?

- Command: `profiler-cli thread markers --session <s> --search launch_application --list --limit 0`
- Expected: to see the part of the message that answers the question (`XpcshellTestRunnerService$iN` and `_TEST_NAME = "..."`).
- Got: the message cut after about 200 characters. `_TEST_NAME` sits several kB into the message, so it is never shown.
- Workaround: `--json` and a jq `capture()` over `.data.message`. Something like `--show-field message --grep <regex>`, or matching context around the `--search` term, would have answered this.

## Did this `ps` listing contain process X?

- Command: `profiler-cli thread markers --search get_process_list --list` (mozdevice DEBUG markers that hold a whole `ps` listing)
- Expected: a way to see whether a term occurs inside each matching marker's payload.
- Got: the first 200 characters of each message.
- Workaround: `--json` and a jq regex over `.data.message`.
