# craft-ai-client

App-facing entry point of the Craft AI layer: resolve a role, check consent, fetch the key, start a job. Native only..

Part of the shared AI layer for the Craft Apps; the contract is in [docs/ai-shared-layer.md](../../docs/ai-shared-layer.md) and the data model in [`craft-ai-model`](../ai-model/README.md).

## Boundaries

- Never crashes: the crate denies `unwrap`, `expect`, `panic` and friends, and the workspace forbids `unsafe`.
- A secret value never reaches `Debug`, `Display`, an error message or a log line.
- No app code: an app is only an `AppId` string.

`AiClient` is what an app holds: `status`, `plan`, consent, `start` and `Job`. `ProviderFactory` maps a provider name to an adapter (OpenAI-compatible, ArtCraft, BFL, Vertex, Seedance, ComfyUI, SAM 3, and declarative routes read from `specs/<connection id>.json`). `ToolDesk` and `ConnectionManager` add, edit, test and remove connections and tool bindings under the shared store's lock. An unavailable adapter is never reported as ready, and an app that lacks one is refused rather than silently given another engine. Custom connections and local sources are described in [docs/ai-custom-connections.md](../../docs/ai-custom-connections.md).

## Platforms

Windows, macOS and Linux (native only). CI runs the suite on all three; no real keychain or live provider is involved.

## Usage

`AiClient` is what an app holds; `examples/custom_tool.rs` runs a declared tool end to end through two independent clients (`cargo run -p craft-ai-client --example custom_tool`).
