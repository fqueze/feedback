## Question: does any bug name this test?

- Command: `fx-tests test devtools/shared/commands/resource/tests/browser_resources_several_resources.js --bugs`
- Expected: a "Bugs" section, saying "none" when no bug names the test.
- Got: output identical to the run without `--bugs`; no line saying whether bugs were searched and none found.
- Workaround: assumed none.
