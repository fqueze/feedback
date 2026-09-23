## `fx-tests test <path> --bugs` with no matching bug

- Command: `fx-tests test browser/components/customizableui/test/browser_886323_buildArea_removable_nodes.js --bugs`
- Expected: a "Bugs" section, or a line saying no bug names the test.
- Got: the same output as without `--bugs`, with nothing about bugs, so I could not tell "none found" from "flag ignored".
- Workaround: took it as "none".
