# craft-ai-comfyui

Local ComfyUI workflow adapter for the Craft AI layer: stored workflows, preflight, uploads, bounded outputs and the SAM 3 clients. Loopback only..

Part of the shared AI layer for the Craft Apps; the contract is in [docs/ai-shared-layer.md](../../docs/ai-shared-layer.md) and the data model in [`craft-ai-model`](../ai-model/README.md).

## Boundaries

- Never crashes: the crate denies `unwrap`, `expect`, `panic` and friends, and the workspace forbids `unsafe`.
- A secret value never reaches `Debug`, `Display`, an error message or a log line.
- No app code: an app is only an `AppId` string.

A local-only workflow adapter for ComfyUI on loopback: stored API-format workflows, a preflight against `/object_info`, uploads, polling, bounded output retrieval, and the SAM 3 point, text and hover clients. Cancellation is off unless the caller confirms prompt-scoped interrupts. Eleven tests are `#[ignore]`d live tests that need a running ComfyUI with SAM 3.

## Platforms

Windows, macOS and Linux wherever ComfyUI runs on loopback (native only). CI runs scripted-server tests on all three; the live SAM 3 tests (`#[ignore]`d) have only been run by hand on one Windows machine with an NVIDIA GPU.

## Usage

Built and registered by `craft-ai-client`'s `ProviderFactory`; `tests/provider.rs` shows the request and result shapes against a scripted server.
