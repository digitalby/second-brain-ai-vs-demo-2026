## Setup reference

1. Install stable Android Studio and its Kotlin Multiplatform plugin. In SDK Manager install the SDK/platform tools required by the generated project and one emulator image. Use the IDE's compatible Gradle JDK and the same JDK in your terminal. Confirm the SDK location through IDE settings; do not copy somebody else's absolute path.
2. Create a Gradle Kotlin Multiplatform project with shared Compose UI and only the agreed target. Do not select Server. Let the current wizard provide a compatible Kotlin/Compose/Android Gradle Plugin/Gradle combination. If a repository already exists, inspect and preserve its working versions.
3. If iOS is explicitly selected, use macOS with Xcode, finish first-launch setup, and install the required simulator runtime. Verify the Kotlin/Xcode compatibility documented for the chosen release. A physical-device run also needs signing configuration.
4. Install Claude Code using its [official setup page](https://code.claude.com/docs/en/setup). On macOS with Homebrew, one documented option is `brew install --cask claude-code`. For Linux/macOS/WSL, the official native installation command is `curl -fsSL https://claude.ai/install.sh | bash`. These are alternatives, not consecutive steps. Windows users should use the current Windows instructions on that page. Inspect the installation method before executing it.
5. In a fresh terminal run `claude --version`, then `claude doctor`. Launch `claude` in the project directory and sign in through the supported account flow. Copy this pack's CLAUDE.md to the project root and the chosen prompt files into `prompts/`. Use the planning prompt first if no plan exists.

The setup above follows [JetBrains' KMP quickstart](https://kotlinlang.org/docs/multiplatform/quickstart.html). The commands are instructions to the reader; assume they were not executed as part of preparing this pack.

## Prompt

Read CLAUDE.md and the approved plan. Preserve existing code and user changes. Record the approved scope in docs/decisions.md. Inspect installed tools before proposing installations. If a required tool is unavailable, report exactly what is missing and provide the relevant official instructions.

Get the generated empty application running on the ONE agreed target before implementing features. Record versions of Kotlin, Compose Multiplatform, Android Gradle Plugin where applicable, Gradle, JDK, SDK, and Xcode where applicable in docs/toolchain.md. Use the wrapper. Inspect `./gradlew projects` and `./gradlew tasks --all` to discover the actual module-qualified build/test/run tasks; Windows may use `gradlew.bat`. Do not assume module names or that a generic task covers every target.

Keep the generated module layout. Establish capture, store, and surface packages and small interfaces: VaultStore, SearchIndex, and SearchRepository. Inject platform adapters into shared state logic. Add no unused abstraction, service, networking dependency, dependency-injection framework, or AI runtime.

Use Compose Multiplatform for UI and kotlinx-coroutines-core for asynchronous state. Add AndroidX SQLite interfaces and sqlite-bundled for the index at the search step. For desktop paths, assess Okio; for an Android selected folder, use the platform document APIs. Pin verified compatible library versions in the version catalog. “Latest” is not a reproducible version.

Document the dependency purpose, target/source set, and published coordinate for each added library. Confirm target coverage from its official documentation. For sqlite-bundled, prove actual FTS5 table creation later in the application target, not just the workstation's SQLite shell.

Run the target build and launch the empty screen if the environment permits. Add the minimal shared test wiring using kotlin.test and kotlinx-coroutines-test. Report the commands, outputs, launch observation, and any unavailable device checks. A successful compile alone is not a launch check.

Stop when the environment works and the architecture boundaries are ready. Do not implement graph, embeddings, RAG, synchronization, or extra platforms. Continue with Store, then Capture, then full-text search; their filenames follow the talk's conceptual order, not build dependency order.
