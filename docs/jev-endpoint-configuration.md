# Decision endpoint configuration

The Jev client accepts two optional environment variables:

| Variable | Default | Meaning |
| --- | --- | --- |
| `WEASEL_JEV_ENDPOINT` | Existing upstream HTTPS decision endpoint | Full HTTPS URL for a compatible decision service. |
| `WEASEL_JEV_MODEL` | `jev-latest` | Model identifier included in the decision request. |

The existing `TYPESAFE_API_KEY` supplies the bearer credential. `WEASEL_JEV_ENABLED` and the mandatory application allow-list `WEASEL_JEV_APPS` retain their existing behavior. Restart the server after changing its environment.

Custom endpoints must use HTTPS, must not embed a username or password, and must not contain a query or fragment. HTTP redirects are rejected so the bearer credential cannot be forwarded to a different destination. TLS certificate verification remains enabled.

The service must accept the existing `state` and `questions.intent` choice request and return `answers.intent` with a declared choice and valid probabilities. Canonicalized response model names do not affect choice validation.

Transport timeouts are bounded at 1 second for resolution/connection, 1.5 seconds for sending, and 2.5 seconds for receiving. Requests remain asynchronous; these are per-operation bounds rather than an overall deadline. A failed or late request leaves ordinary Rime input behavior in place.

## Optional local diagnostics

`WEASEL_JEV_DIAGNOSTIC_LOG` selects an append-only local diagnostic file. It works independently of release logging macros and records startup readiness, request completion, and selection validation. It is disabled when unset.

`WEASEL_JEV_LOG_DECISIONS=1` additionally records the returned choice, confidence (when supplied), and probability map. `WEASEL_JEV_LOG_CANDIDATES=1` adds candidate text and the raw-input choice to those decision events. Candidate logging can reveal private input: enable it only for deliberate tests, keep logs outside the repository, and disable it afterward. Context text, authorization headers, and credentials are not logged. Existing logs are not retroactively enriched. Probability values are serialized as JSON strings by Boost.PropertyTree.

Keep deployment URLs, model selections, credentials, local launch scripts, and captured request content outside the repository. Prefer process-scoped credentials over permanently storing a bearer token in the user environment. Enable cloud processing only for applications whose input may be sent to the selected service.
