## Question: where was the hung child stuck, across all the hang dumps of this test?

- Commands: `fx-tests test <path> --task-ids --limit 0` (94 `fx-tests crash <task> <dump>` lines), then one `fx-tests crash <task> <dump> --all-threads --frames 60` per dump in a shell loop, grepping the MainThread and Renderer blocks for `nsAppShell::Init`, `RenderThread::ShutDown`, `CGLChoosePixelFormat`, `_LS`.
- Expected: a way to group a test's crash/hang dumps by a stack-derived key (e.g. top in-Gecko frame of the crashing thread, plus which process type), with counts.
- Got: one dump at a time; 94 sequential calls.
- What could have shown it: `fx-tests test <path> --crash-summary` (or `fx-tests crash --task-ids-from <path>`) grouping dumps by the first non-system Gecko frame on the crashing thread and process name (here: "Firefox Nightly GPU Helper").

## Question: what is the source at the failing revision? (review-test_FirefoxLabs.js)

- Command: `fx-tests task fABqTAEES1ioUmmBwJounw.0 --profiles` prints `autoland 65844140c333` (hg), then `curl -sL https://hg.mozilla.org/integration/autoland/raw-file/65844140c3337a44cf2c06eacf82edab905f9fbb/<path>`.
- Expected: the file at that revision.
- Got: HTTP 406, with or without a User-Agent or Accept header (the report's author hit it too and fell back to origin/main).
- Workaround: `curl -sL https://lando.moz.tools/api/hg2git/firefox/<hg sha>` gives the git SHA (28fa22e736b6), which was already in the local checkout, so `git show <sha>:<path>` worked.
- What could have shown it: `fx-tests task` printing the git SHA next to the hg one.
