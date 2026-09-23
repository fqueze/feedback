## `fx-tests test <path> --bugs` prints no bug section at all
- Command: `fx-tests test browser/components/places/tests/unit/test_browserGlue_corrupt_nobackup.js --bugs`
- Expected: a "Bugs" section listing bugs naming the test, or an explicit "no bug names this test".
- Got: the normal summary only. I could not tell whether it searched and found nothing, or ignored the flag.
- Workaround: Bugzilla REST `bug?summary=<test file>` and `bug?summary=Storage connection not closed`. That found bug 2061660, the generic bug for this crash message.

## Question: "which revision to read the code at", when hg.mozilla.org is unusable
- Command: `fx-tests task UU8IoiQLTQCZFBLbgh9g3g.0` prints `autoland 1ff3ffd34ab8` (an hg hash).
- `curl -sL https://hg.mozilla.org/integration/autoland/raw-file/1ff3ffd34ab8/<path>` returns HTTP 406, with an empty body, today.
- Workaround: `https://lando.moz.tools/api/hg2git/firefox/<hg>` gave git `91f409f7391c…`, then `git show <git>:<path>` worked locally.
- What the output could have shown: the git hash next to the hg one.

## Question: "which DB connection was left open" — crash annotations not shown
- Command: `fx-tests crash UU8IoiQLTQCZFBLbgh9g3g.0 b286ae65-… [--json]`
- Expected: crash annotations, here `StorageConnectionNotClosed`, which names the database.
- Got: signature and stacks only. `--json` has no annotations key.
- Workaround: the "Storage connection not closed: <file>" stdout line, found in the resource-usage profile. `--all-threads` thread names (`sqldb:places.sqlite #2`) were the key finding here, and those worked well.
