## Log markers (mochitest INFO) print "(empty)" for their message

- Command: `profiler-cli thread markers --thread t-18 --category Test --list --limit 0 --session test_bug331959.html-1`
  and `profiler-cli marker info m-307 --session test_bug331959.html-1`
  (profile: https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/RvTP9NRFTcyoxaOUB4101w/runs/0/artifacts/public/test_info/profile_test_bug331959.html.json)
- Expected: the INFO line's text, e.g. `must wait for focus`, `Error: Unable to restore focus, expect failures and timeouts.`
- Got: `[(empty)] INFO: (empty)` in the list, and `Level: (empty)` / `Message: (empty)` in `marker info`.
  `--json` shows `"value": "must wait for load", "formattedValue": "(empty)"`: the value is there, the formatting drops it.
- Workaround: `--json` and jq over `.flatMarkers[].fields[] | select(.key=="message").value`.
  The question it could not answer: "what did the harness log for this test before it timed out" — the single most useful line
  in this investigation ("Unable to restore focus") was invisible in the default output.

## `profile markers --search` does not match the message of Log (mochitest INFO) markers

- Command: `profiler-cli profile markers --search "Unable to restore focus" --session test_bug331959.html-3`
  (profile: https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/FW58XVpxQTS1nW4RZ6d1fg/runs/0/artifacts/public/test_info/profile_test_bug331959-2.html.json)
- Expected: the INFO marker on the content GeckoMain whose `message` is `Error: Unable to restore focus, expect failures and timeouts.`
- Got: `No markers match the specified filters (searched 19 threads).`
- Workaround: `thread markers --thread <content main> --category Test --list --limit 0 --json` and jq over `fields[].value`.
  Probably the same root as the "(empty)" formatting above: search runs on the formatted value.
