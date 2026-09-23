## `fx-tests task <id>` blames expiry when run 0 was an exception

- Command: `fx-tests task ZnpEvNYJR265Mcda_YWPIw --profiles`
- Expected: a pointer to the run that has artifacts (run 0 was `exception` / worker-shutdown, run 1 completed on 2026-09-21).
- Got: "has no profile_resource-usage.json: the artifact is not there. Taskcluster expires task artifacts after about a month, so this is permanent and retrying will not help." The task was one day old.
- Workaround: read `/status` from the Taskcluster queue, then `fx-tests task ZnpEvNYJR265Mcda_YWPIw.1`.

## Question: "which job on the same push ran this manifest on another config?"

- Context: the failing config (linux tsan) uploads no per-test profiles, so I needed the same manifest's job on a non-tsan config of the same push to get one.
- What I did: fetched `public/label-to-taskid.json` from the decision task group, then read each chunk's `MOZHARNESS_TEST_PATHS` from its task definition (15 requests) to find the chunk.
- What could have answered it: `fx-tests task <taskId> --same-push <config>` (or a list of sibling jobs of the push that ran the manifest), with their per-test profile URLs.

## `fx-tests test <path> --bugs` is silent when no bug names the test

- Command: `fx-tests test browser/components/customizableui/test/browser_934113_menubar_removable.js --bugs`
- Expected: an explicit "Bugs naming this test: none".
- Got: the normal output with no Bugs section, which reads the same as the flag being ignored.
