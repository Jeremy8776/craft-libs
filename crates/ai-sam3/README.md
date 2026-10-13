# craft-ai-sam3

Local SAM 3 object detection adapter for Craft AI.

Part of the shared AI layer for the Craft Apps; the contract is in [docs/ai-shared-layer.md](../../docs/ai-shared-layer.md) and the data model in [`craft-ai-model`](../ai-model/README.md).

## Boundaries

- Never crashes: the crate denies `unwrap`, `expect`, `panic` and friends, and the workspace forbids `unsafe`.
- A secret value never reaches `Debug`, `Display`, an error message or a log line.
- No app code: an app is only an `AppId` string.

A loopback-only client for a SAM 3 text-prompted object detection bridge (`bridge/server.py`, which needs a separately acquired SAM checkpoint). Whether the Python bridge belongs in this repo is open for review; the ComfyUI route in `craft-ai-comfyui` is the supported path.

## Platforms

Windows, macOS and Linux (native only; not for wasm). Run in CI on all three against scripted localhost servers. The Python bridge (`bridge/server.py`) needs Python and a separately acquired SAM checkpoint; it has not been run in CI.

## Usage

Built and registered by `craft-ai-client`'s `ProviderFactory`; `tests/provider.rs` shows the request and result shapes against a scripted server.
