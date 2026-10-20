# Tests and the weekend acceptance gate

Use after the baseline loop, and again after an approved extension. This prompt requests actual verification. It does not authorize destructive tests on a real vault.

## Prompt

Read CLAUDE.md, docs/decisions.md, docs/toolchain.md, and the implementation. Review the approved acceptance criteria before adding tests. Use a disposable synthetic vault and keep a record of fixture files created. Never delete or corrupt the user's real notes to test recovery.

Create a concise evidence matrix with requirement, test type, target/environment, command or manual steps, observed result, and status. Valid statuses are Passed, Failed, Not run, and Out of scope. Do not count a test that was only written as Passed. Do not count a simulated platform adapter as a device check.

Cover these behaviors with the smallest meaningful combination of tests:

| Area | Evidence to collect |
| --- | --- |
| Shared logic | Empty input, save/error state transitions, duplicate submission, ID collision handling, metadata preservation, and deterministic clocks. |
| Real Store adapter | Exact Unicode/line-break persistence; create/update; interrupted replacement recovery; conflict handling; unavailable/read-only storage; Android permission revocation if targeted. |
| Real search driver | FTS5 availability, expected keyword/phrase results, query punctuation, ranking direction, source edits/deletions, unreadable-file handling, stale-index repair, and complete rebuild from Markdown. |
| UI and lifecycle | Actual capture route, failure message with intact draft, activity recreation where relevant, restart, and reopening the saved note. |
| Offline behavior | After dependencies are installed, capture/read/search with networking unavailable. Check the selected provider is local; airplane mode alone does not prove an app never transmits when online. |
| Scope inspection | Dependency list, merged Android manifest if relevant, permissions, backup policy, and network-call paths agree with the local-only baseline. |

Use kotlin.test and kotlinx-coroutines-test for shared behavior. Use real temporary files and a real bundled SQLite database for integration tests. For Android use the project's supported instrumented/Compose test stack on a named emulator/device. Discover module-qualified Gradle tasks rather than copying generic commands. Run only selected target checks and record what they cover. If iOS was selected, build/run through the verified Xcode configuration and identify simulator versus physical device.

Perform this end-to-end check: type/share “California packing: bring the USB Type-C charger”; save once; inspect its .md file externally; restart; search “charger”; open the exact note; remove only the disposable derived index; rebuild; repeat the query; verify source-file hashes did not change. Then inject a write failure and show that the old committed note and retryable input remain available. Record actual observations.

If embeddings are in scope, add model/tokenizer parity, stale-vector invalidation, measured retrieval quality, and memory/latency on the actual hardware. If RAG is in scope, inspect cited passages for evidence support, abstention, stale-source handling, and malicious-note resistance. Keep deterministic mocked tests separate from real-model evaluations. Neither proves a security audit.

Inspect the code as well as tests, especially file replacement, permission handling, migration, database lifecycle, and dependencies. Do not weaken a failing assertion just to get green output. Fix within the approved scope and rerun the affected checks. Record unrelated failures separately.

Write docs/verification.md with the evidence matrix, exact commands/environments, failures, unrun checks, and limitations. If hardware or SDKs are unavailable, give the precise remaining manual steps without claiming completion. Finish with a truthful go/no-go for the agreed personal-prototype use case. Do not publish or distribute the app as part of this check.
