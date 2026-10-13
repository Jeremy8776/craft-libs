# craft-ai-declarative

Runs a declarative spec: a provider described by a small data file, for Craft AI.

Part of the shared AI layer for the Craft Apps; the contract is in [docs/ai-shared-layer.md](../../docs/ai-shared-layer.md) and the data model in [`craft-ai-model`](../ai-model/README.md).

## Boundaries

- Never crashes: the crate denies `unwrap`, `expect`, `panic` and friends, and the workspace forbids `unsafe`.
- A secret value never reaches `Debug`, `Display`, an error message or a log line.
- No app code: an app is only an `AppId` string.

Runs a route described as data (`docs/ai-declarative-adapter.md`): request/response and queue flows against a service that is not OpenAI-shaped, same-origin only, one header for the key.

## Platforms

Windows, macOS and Linux (native only; not for wasm). Run in CI on all three against scripted localhost servers.

## Usage

Registered by `craft-ai-client` for connections whose provider is `declarative`; the route comes from a data file (see [docs/ai-declarative-adapter.md](../../docs/ai-declarative-adapter.md)).
