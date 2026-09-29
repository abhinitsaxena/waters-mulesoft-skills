# Integration Tests — Rules

When running end-to-end integration tests on a deployed app, follow these conventions:

- **Not MUnit**: integration tests are HTTP-based tests against a deployed application. Do not conflate with MUnit (in-process unit tests).
- **Credentials source**: always take `client_id`/`client_secret` (and `dd_api_key`/`dd_app_key` for observability cases) from `curie/testData/{{env}}/creds/creds.json` **only**. Never read inbound credentials from the env YAML (`http.*.clientId` are outbound SF/batch creds).
- **No "NA" creds**: if `creds.json` holds `"NA"` or is missing for an env, **stop and ask** for the real values. Do not proceed or substitute another env's creds.
- **Fire-and-forget model**: a `201 {"message":"Job Triggered"}` confirms the trigger, not the SFTP file delivery. Verify actual delivery via the observability case (TC02) and the SFTP target.
- **Test flag**: send `postmanTestFlag: true` to end the run before the schedule-stop call.
- **Environment restrictions**: only target runnable environments (`dev`, `stage`). Never target `prod`, `preprod`, or `-s4` environments.
- **No secrets in output**: never log, echo, or include credentials in chat or test output.
- **Case references**: always reference test cases by their `TCnn` identifier from `curie/knowledge/integration-test-cases.md`.
