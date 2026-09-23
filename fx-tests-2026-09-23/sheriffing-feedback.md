# Sheriffing and Bugzilla data issues (from fx-tests feedback)

Problems in sheriff annotations and bug metadata that fx-tests reports rely on, from the same feedback files. Counts are the number of reports. Sources for each item: [sheriffing-feedback-sources.md](sheriffing-feedback-sources.md).

## 1. Sheriffs star failures on the wrong bug (26)

Many annotations belong to other failures: 68 of 69 on bug 2057939 are geckoview-reftest failures, 32 of 55 match bug 1976612 instead, 17 of 247 on bug 2071883 are apt/ftp/mirror errors, 58 of 167 'max run time' stars were memory-guard kills. Sibling-test bugs attract stars too.

*Suggestion:* Show the bug's expected failure line when starring and warn when the starred job's lines don't match it.

## 2. Tracking bug summaries keep a test's old path after rename or move (15)

Intermittent bugs such as 1773790, 1875918, 1767651, 1378104 and 2063730 still name the pre-rename path, so they aren't matched to the current test and history is split across old and new paths while sheriffs keep starring on them.

*Suggestion:* Update intermittent bug summaries (and bug-to-path mappings) when a test is renamed or moved.

## 3. Real failures left unannotated by sheriffs (12)

Failures matching open bugs often aren't starred: 12 identical awsy-tp6 failures in one week, 60+ yelp timeouts, 17 failures of bug 1110922's test, aarch64 backlog jobs, WPT TEST-KNOWN-INTERMITTENT-FAIL results, so annotation counts undercount failures.

## 4. Same failure starred on several bugs, splitting counts (3)

The same ScriptTimeout failure is starred on bugs 2052509 and 1924784; after a harness change failures went to 2070794, 2071660, 2070405.

*Suggestion:* Dupe the bugs.

## 5. Crash bugs lack cf_crash_signature (2)

Bug 2009527 (WorkerPrivate assertion) names neither the test nor has cf_crash_signature set.

*Suggestion:* Set cf_crash_signature on crash bugs.

## 6. Failures passing on harness retry are never starred (2)

Retry-passing failures leave jobs green, so bug 1994039 has no annotations.

## 7. Manifest skip-if still cites a closed bug (1)

test_ext_identity.html's manifest skip-if cites bug 1777016, a closed single-tracking bug.
