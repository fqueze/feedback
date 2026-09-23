## (review) Did the push schedule the variant the removed skip-if targets?

- Command: `fx-tests try 2888bcab0070 --test devtools/client/netmonitor/test/browser_net_copy_as_powershell.js --all-jobs`
- Expected: a note that the removed skip-if (`... && debug && http3`) matches no job on the push, so the skipped config never ran.
- Got: only the jobs that ran the test. You can't tell a missing variant apart from a clean pass, and the report took "none failed" as "no longer fails".
- Workaround: pulled the push's job list from the Treeherder API (`/api/jobs/?push_id=…`) and grepped it for `http3`.
