## `fx-tests task --profiles` omits the job's shutdown profile
- Command: `fx-tests task Qo2X10_8QriebUHIcY_0bA.0 --profiles`
- Got: the resource-usage profile and the per-test profile only.
- The job also uploaded `profile_devtools_client_framework_test_browser_shutdown.json` (346 MB, from an AsyncShutdown timeout caused by the same hung content process); found it only in the resource-usage profile's `artifact` markers.
- Expected: listed with the failure it belongs to (the AsyncShutdown ERROR line).

## `fx-tests test <path> --bugs` prints nothing when no bug matches
- Command: `fx-tests test devtools/client/framework/test/browser_toolbox_broken_xml_frame.js --bugs`
- Got: the normal summary, no bug section and no "no bug names this test" line; cannot tell "none found" from "flag ignored".
