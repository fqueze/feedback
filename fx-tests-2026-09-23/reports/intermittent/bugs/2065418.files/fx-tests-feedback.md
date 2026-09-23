# fx-tests feedback (bug 2065418)

## Question: which failure did each starred build job hit?

Command: `fx-tests intermittent --bug 2065418 --tree all --since 21`

Expected: a breakdown of the 195 starred jobs by failure message, as for test bugs.

Got: "Failure messages, per annotated job" listed 5 lines, all from the one misstarred
mochitest job. The other 194 rows are `[taskcluster:error]` build jobs, and every one has
`"lines": []` in `--json`. So the section suggests the bug is about siteIdentity tests, when
194 of 195 stars are snap build jobs, and it gives no way to tell apart the 9 different
failures sheriffs starred on this bug.

Workaround: `--json --limit 0`, then download all 195 `live_backing.log` files (663 MB) and
classify them with a script (`classify2.py` here).

What would have answered it: for jobs without parsed failure lines, the first `E: ` /
`error` line near the end of the log, or at least a note that N of M occurrences have no
failure line.
