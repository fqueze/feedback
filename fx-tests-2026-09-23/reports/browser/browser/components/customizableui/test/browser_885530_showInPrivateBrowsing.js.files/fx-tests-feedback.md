## Question: "what source revision do I read the test at?" (fx-tests task)
- Command: `fx-tests task KOtfBM-ATXujAr_XkCQOOg --profiles`
- Expected: the revision in a form I can read locally (the git hash, since the checkout is git), or both hg and git.
- Got: `autoland 65844140c333` (hg only). `curl https://hg.mozilla.org/integration/autoland/raw-file/<rev>/...` returned HTTP 406 for every URL, and `git cat-file` does not know the hg hash.
- Workaround: `curl https://lando.moz.tools/api/hg2git/firefox/<hg>` gave git 28fa22e736b6, then `git show <git>:<path>`.

## `--bugs` prints nothing when there is no bug
- Command: `fx-tests test browser/components/customizableui/test/browser_885530_showInPrivateBrowsing.js --bugs`
- Expected: a "Bugs: none annotated" line.
- Got: the normal output, with no Bugs section at all. I could not tell "no bugs" from "the lookup silently failed".
- Workaround: searched Bugzilla REST by summary.

## Question: "which tests in this manifest fail together on this config?"
- Commands: `fx-tests test <each neighbouring test>`, run in a shell loop over 9 tests
- What would have answered it: a manifest-level (or `--neighbours`) view listing, for one config, each test's fail rate and first message, in manifest order. That would show the cluster at once: 876926 `sess is null` on every config that runs it, and 884402…892956 at 50% on mac opt with the vsync message.
