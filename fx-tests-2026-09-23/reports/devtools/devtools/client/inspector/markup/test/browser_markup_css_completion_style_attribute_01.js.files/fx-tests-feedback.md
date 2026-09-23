# fx-tests feedback

## `--bugs` prints nothing when no bug is found

- Command: `fx-tests test devtools/client/inspector/markup/test/browser_markup_css_completion_style_attribute_01.js --bugs`
- Expected: a "Bugs" section, or a line saying no bug names this test.
- Got: output identical to the command without `--bugs`, so I could not tell whether the search
  ran and found nothing, or did not run.
- Workaround: Bugzilla REST quicksearch on the test file name (returned no bug).
