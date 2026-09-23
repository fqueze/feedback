## `fx-tests test <path> --bugs` prints no bug section when there are none

- Command: `fx-tests test browser/components/resistfingerprinting/test/browser/browser_cross_origin_isolated_reduce_time_precision.js --bugs`
- Expected: a "Bugs" section, or a line saying no open bug names the test.
- Got: the same output as without `--bugs`, ending at "Issues". It did not say whether it searched, nor mention the RESOLVED INCOMPLETE tracking bug 1781850 that names the test.
- Workaround: Bugzilla REST `bug?summary=<test file name>`.
