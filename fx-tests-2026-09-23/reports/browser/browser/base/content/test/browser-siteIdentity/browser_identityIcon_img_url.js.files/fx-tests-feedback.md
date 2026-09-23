## `test --bugs` prints nothing when no bug matches

- Command: `fx-tests test browser/base/content/test/siteIdentity/browser_identityIcon_img_url.js --bugs` (same for the new path)
- Expected: a line such as "Sheriff-annotated bugs: none".
- Got: output identical to running without `--bugs`, so I could not tell "no bugs" apart from "flag ignored". I had to check `--json` (`annotatedBugs: []`).
- Workaround: `--json`, then a Bugzilla REST summary search.

## `errors --message` does not list the tests behind a message

- Question: "which tests emit this message?"
- Command: `fx-tests errors --message 'Component is not available"  nsresult: "0x'`
- Expected: per the guide ("Lists the tests emitting one message"), the tests behind the row.
- Got: one aggregated row (33,439 occurrences, 36 tests) and no test names. It needed `--group-by test`.
- Also: the message filter matches raw text, but the table shows `0x...`-normalized text. Filtering on the displayed text (`(NS_ERROR_NOT_AVAILABLE)"  location: "<unknown>"`) worked, while `location: "<unknown>"  data: no]` matched unrelated messages.
