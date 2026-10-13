# craft-ai-features

What the Craft apps can do with AI, how each tool can be run, and what a machine still needs. Wasm-safe..

Part of the shared AI layer for the Craft Apps; the contract is in [docs/ai-shared-layer.md](../../docs/ai-shared-layer.md) and the data model in [`craft-ai-model`](../ai-model/README.md).

## Boundaries

- Never crashes: the crate denies `unwrap`, `expect`, `panic` and friends, and the workspace forbids `unsafe`.
- A secret value never reaches `Debug`, `Display`, an error message or a log line.
- No app code: an app is only an `AppId` string.

The catalogue of AI tools (features), their setup options and plans, and the model recipes (pinned files with size and SHA-256).

## Platforms

All platforms; `wasm32-unknown-unknown` is checked in CI. Native CI on Linux, Windows and macOS.

## Usage

`Feature` and its recipes are plain data: `craft-ai-client` and the apps read them to decide what a tool needs. See `tests/` for the catalogue invariants.
