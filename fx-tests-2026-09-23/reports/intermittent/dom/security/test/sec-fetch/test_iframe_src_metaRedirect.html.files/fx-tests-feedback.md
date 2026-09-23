## Question: "a passing run of the same job, to compare with a failing one"

- Command: `fx-tests test dom/security/test/sec-fetch/test_iframe_src_metaRedirect.html --task-ids --limit 0`
- Expected: a way to get task IDs of *passing* runs of a failing config (e.g. `--task-ids --passing --config <job>`), to load their resource-usage profiles next to the failing ones.
- Got: only failing task IDs; no option lists passing runs.
- Workaround: Treeherder REST `jobs/?push_id=..&job_type_name=..` to get the job id, then `jobs/<id>/similar_jobs/` for neighbouring passing task IDs (several requests, and the `jobs` endpoint is paged, so the first attempt missed the job).
