## Question: which tests and crash signature are behind this bug, and on which configs?

- Command: `fx-tests intermittent --bug 2036027`
- Expected: the bug's crash signature and the test it crashes in, with the unstarred occurrences of the same signature (the Windows ones).
- Got: 3 macOS annotations only; "Tests named" lists `leakcheck`, `ShutdownLeaks` and another test's name, never `browser_toolbox_backward_forward_navigation.js`, where all 57 `JSAsyncTaskStarted` crashes are (`fx-tests crashes` shows 57 crashes in 1 test).
- Workaround: `fx-tests task <taskId>` on an occurrence named the test, then `fx-tests test <path>`. Linking the bug's assertion text (`WorkerPrivate.cpp:X`) to the crash signature and its test would have answered it.
