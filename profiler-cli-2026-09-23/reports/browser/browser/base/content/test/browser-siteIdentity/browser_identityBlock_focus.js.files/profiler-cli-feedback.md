## Question: where was this "uncaught rejection" created?

- Command: `profiler-cli marker stack m-16 --session browser-browser_identityBlock_focus.js-1` on the `TEST-UNEXPECTED-FAIL | uncaught rejection: ... NS_ERROR_NOT_AVAILABLE` marker.
- Expected: something pointing at the code that rejected the promise.
- Got: the harness's reporting stack (`PromiseTestUtils.assertNoUncaughtRejections` / `nextTest`), 150 ms after the fact. The rejection's origin was only found by listing every marker in the 200 ms before it, excluding a dozen noisy names by hand, and spotting `MozPromise::ThenValueBase::ResolveOrRejectRunnable MultiGetUntrustedModulesData` immediately followed by `dom::FlushRejections`, `NotifyUnhandledRejections`, `dom::AsyncErrorReporter`, and the `Console message: NS_ERROR_NOT_AVAILABLE` log.
- What could have shown it: for an uncaught-rejection failure (or a `NotifyUnhandledRejections` runnable), name the runnable that ran just before the `dom::FlushRejections` it follows — i.e. the task in which the promise was rejected without a handler.
