## `--bugs` prints nothing when no bug names the test
- Command: `fx-tests test browser/components/enterprisepolicies/tests/browser/browser_policy_sitepolicies_jit.js --bugs`
- Expected: a "Bugs" section, saying "none" when none is found.
- Got: the same output as without `--bugs`, so I couldn't tell whether the search ran. 
- Workaround: a Bugzilla REST quicksearch with curl.
