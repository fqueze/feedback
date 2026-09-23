## `fx-tests test <path> --bugs` shows no bug section at all

- Command: `fx-tests test browser/components/extensions/test/browser/browser_ext_menus_viewType.js --bugs`
- Expected: a "Bugs" section naming the bugs whose summary names the test, or an explicit "no bug found" line. Bugzilla has five for this file, the latest being bug 1775564 "Intermittent browser/components/extensions/test/browser/browser_ext_menus_viewType.js | single tracking bug" (RESOLVED INCOMPLETE).
- Got: output identical to `fx-tests test <path>` without the flag; nothing says whether the lookup ran, found nothing, or found only closed bugs.
- Workaround: `curl -sL "https://bugzilla.mozilla.org/rest/bug?summary=browser_ext_menus_viewType&include_fields=id,summary,status,resolution"`.
- Question it should answer: "which bug tracks this test, and is it still open?" Listing closed tracking bugs with their resolution would also tell whether a new one is needed.
