
## review-browser_webextension_dropdown.js: `stop` takes only one session

- Command: `profiler-cli stop review-browser_webextension_dropdown.js-2 review-browser_webextension_dropdown.js-3`
- Expected: both named sessions stopped (`marker info` already takes several handles).
- Got: `error: too many arguments for 'stop'. Expected 1 argument but got 2`.
- Workaround: one `stop` call per session.
