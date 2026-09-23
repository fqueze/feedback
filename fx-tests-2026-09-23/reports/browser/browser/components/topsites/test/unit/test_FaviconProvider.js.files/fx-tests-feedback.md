## `failures --message` does not find a crash message that `test` and `crashes` show

- Command: `fx-tests failures --message "child process hang at shutdown"`
- Expected: the tests behind the message `fx-tests test` lists under Issues as `CRASH child process hang at shutdown`.
- Got: "No failure matched. Searched 4,982 tests in xpcshell-issues.json".
- Workaround: `fx-tests crashes` lists it as a signature (19,349 crashes, 503 tests). A hint in the `failures` "no match" output that the text is a crash signature, to be looked up with `crashes --signature`, would have saved a round trip.

## Question: in which process and frame are all the dumps of one test?

- Question: across the 75 hang dumps of one test, which process hung and where (to tell one mechanism from several).
- Command: a shell loop over `fx-tests crash <task> <dump> --frames 0` for every `fx-tests crash` line printed by `fx-tests test <path> --task-ids --limit 0`, grepping each output for the process name and a few frames.
- What could have shown it: a per-test dump summary (`fx-tests test <path> --dumps`?) grouping the dumps by process type and the top in-Gecko frame of the crashing thread — here 70 × GPU Helper in `nsAppShell::Init`, 5 × GPU Helper in `GPUParent::ActorDestroy`.
