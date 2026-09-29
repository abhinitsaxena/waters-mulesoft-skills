# MUnit Tests — Rules

When writing or updating MUnit test cases, follow these conventions:

- **Resolve all inputs from user**: never silently guess which flow to test, which scenarios to cover, or which test data to use. Ask the user for missing required inputs one at a time.
- **Confirmation before generating**: print a test plan summary (flow under test, mode, scenarios, test data, target suite) and wait for user confirmation before generating any XML.
- **Mock all externals**: every HTTP request, SFTP/FTP write, and external connector call must be mocked. Never let MUnit hit a live system.
- **Match existing style**: read existing test suites under `src/test/munit/` to understand the current mocking style, assertion patterns, and naming conventions before generating new tests.
- **`mock-when` targeting**: use `with-attributes` matching on `doc:id`, `doc:name`, `config-ref`, or `method` as appropriate.
- **`verify-call` alignment**: keep `verify-call` `doc:id` references aligned with the actual logger `doc:id`s in the flow XML. Always cross-check against the current flow XML.
- **Scenario coverage**: every test suite must cover at least one happy-path scenario and one error/edge-case scenario.
- **Secure properties**: ensure `mule.env` and the secure-properties decryption key are set correctly for MUnit so encrypted properties resolve without `BadPaddingException`.
- **Scope boundary**: this skill produces MUnit XML (in-process unit tests), not HTTP-based integration tests. Do not conflate the two.
- **Only this skill reads `src/test/**`**: all other skills treat the test directory as out-of-scope.
