# Other services (from fx-tests feedback)

Problems agents hit with services used next to fx-tests (hg.mozilla.org, Treeherder API, Lando), from the same feedback files. Counts are the number of reports. Sources for each item: [other-tools-feedback-sources.md](other-tools-feedback-sources.md).

## 1. hg.mozilla.org answers HTTP 406 to raw-file, json-rev and json-pushes requests (36)

hg.mozilla.org (via hg-edge redirect) returns HTTP 406 with an empty body for raw-file, json-rev and json-pushes, often after the first few requests, for any User-Agent. Agents cannot read test source or manifests at an autoland or mozilla-central revision.

*Suggestion:* Stop answering 406 to scripted clients, or document the accepted headers/rate limit.

## 2. Treeherder jobs API silently ignores repo and timestamp filters (5)

Treeherder /api/jobs/ ignores repo= and start_timestamp__gte / submit_timestamp__gt, and bug-job-map ignores repo=mozilla-central, returning try jobs.

*Suggestion:* Reject or honour unknown filters instead of silently ignoring them.

## 3. Treeherder per-push jobs endpoint truncates at 2000 jobs (3)

The jobs endpoint silently truncates at count=2000.

*Suggestion:* Return pagination info or an error when truncating.

## 4. Treeherder failure-line history retained only ~120 days (2)

Treeherder failurecount history stops around 2026-05-25, looking like a step change.

## 5. Lando hg2git rejects short revision hashes (2)

As a fallback for hg's HTTP 406, lando hg2git returns 404 for short hg hashes, so revisions from logs can't be mapped to git.

*Suggestion:* Accept short hashes in hg2git.

## 6. live_backing.log artifact served gzip-encoded (1)

A live_backing.log artifact was served with gzip content-encoding, needing curl --compressed to read.
