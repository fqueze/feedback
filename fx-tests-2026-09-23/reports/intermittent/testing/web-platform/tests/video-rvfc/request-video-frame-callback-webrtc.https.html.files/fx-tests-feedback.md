# fx-tests feedback (request-video-frame-callback-webrtc.https.html)

- `fx-tests test testing/web-platform/tests/video-rvfc/request-video-frame-callback-webrtc.https.html` (and `--history`)
  Expected: rates per config for a WPT test. Got: "No test path in the xpcshell and mochitest 21-day data contains ...".
  Workaround: `fx-tests intermittent --bug 1851494 --since 30 --json` for occurrences, plus
  `treeherder-cli --similar-history <jobId> --json` to count runs of the chunk per day as a denominator.
  Question that had no answer: "how many times did this WPT test run on macosx1500-aarch64 opt/debug, and how often did it fail".

- `fx-tests intermittent --bug 1851494`: the text output has no machine name and no per-occurrence failure line for WPT
  (the "Tests named" section says none carried TEST-UNEXPECTED-FAIL, because WPT timeouts are TEST-UNEXPECTED-TIMEOUT / -OK).
  Question: "which failure message did each occurrence have, and on which worker". Needed `--json` (occurrenceRows[].machineName)
  and downloading all 69 live_backing.log to split UNEXPECTED-TIMEOUT (54) from UNEXPECTED-OK (15).
  Could have shown: the status line (TEST-UNEXPECTED-TIMEOUT/-OK) per occurrence, and machineName grouping.

- `fx-tests task ICQwKwRwRE2-EuwYqZguHA`: for WPT the FAILED list mixes expected ERROR/FAIL results with the one unexpected
  result; the rvfc TIMEOUT only appeared with `--limit 0`, with no expected/unexpected distinction.
  Question: "which results in this job were unexpected". Could have shown: an "unexpected" flag per row.
