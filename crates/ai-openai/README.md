# craft-ai-openai

OpenAI-compatible provider for the Craft AI layer: text (streaming, tools, structured output) and images (generate, edit, inpaint). Native only..

Part of the shared AI layer for the Craft Apps; the contract is in [docs/ai-shared-layer.md](../../docs/ai-shared-layer.md) and the data model in [`craft-ai-model`](../ai-model/README.md).

## Boundaries

- Never crashes: the crate denies `unwrap`, `expect`, `panic` and friends, and the workspace forbids `unsafe`.
- A secret value never reaches `Debug`, `Display`, an error message or a log line.
- No app code: an app is only an `AppId` string.

It speaks the OpenAI-compatible API shape (text with streaming, tools and structured output; image generate, edit and inpaint) to any base URL the connection names. The key is sent only in the `Authorization` (or named) header to that address, redirects are never followed.

## Platforms

Windows, macOS and Linux (native only; not for wasm). Run in CI on all three against scripted localhost servers.

## Usage

Built and registered by `craft-ai-client`'s `ProviderFactory`; `tests/provider.rs` shows the request and result shapes against a scripted server.
