## `--bugs` finds nothing, and says nothing

- Command: `fx-tests test browser/base/content/test/favicons/browser_favicon_svg.js --bugs` (and the same for the new `browser-favicons` path)
- Expected: a Bugs section listing bug 2012624 ("Intermittent browser/base/content/test/favicons/browser_favicon_svg.js | single tracking bug", RESOLVED by BugBot in July, with 1 annotation in the window on mozilla-beta), or a line such as "no open bug names this test (1 closed: 2012624)".
- Got: the normal `test` output with no Bugs section and no line saying none was found, so "no bug" and "the flag did nothing" look the same.
- Workaround: `curl 'https://bugzilla.mozilla.org/rest/bug?summary=browser_favicon_svg'`.

## Question (review): the git revision of a job, to read the test as it ran

- Command: `fx-tests task IG3bDLrwQIWKAzzyxSje9g --profiles` prints `autoland ad9dc82bc357` (an hg hash).
- Needed: the source at that revision. `https://hg.mozilla.org/integration/autoland/raw-file/<rev>/<path>` now returns HTTP 406 from hg-edge, and the local git checkout does not know the hg hash. The workaround was `curl https://lando.moz.tools/api/hg2git/firefox/<hg sha>` followed by `git show <git sha>:<path>`.
- What would have answered it: have `fx-tests task` print the git sha as well, or a ready-to-use source URL.
