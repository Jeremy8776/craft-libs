# craft-ai-comfy-env

Finds an existing ComfyUI install, inventories its models and node packs, and downloads missing model files safely. Native only..

Part of the shared AI layer for the Craft Apps; the contract is in [docs/ai-shared-layer.md](../../docs/ai-shared-layer.md) and the data model in [`craft-ai-model`](../ai-model/README.md).

## Boundaries

- Never crashes: the crate denies `unwrap`, `expect`, `panic` and friends, and the workspace forbids `unsafe`.
- A secret value never reaches `Debug`, `Display`, an error message or a log line.
- No app code: an app is only an `AppId` string.

Finds an existing ComfyUI install, inventories its model files, downloads recipe files with resumable, size- and SHA-256-checked transfers (the token goes only to the configured host; every redirect hop passes the shared link rules), installs node packs from hostile-by-default archives, and can unpack the pinned official portable build. Nothing it downloads is run.

## Platforms

Windows, macOS and Linux (native only). CI runs scripted folder trees and servers on all three; a real ComfyUI install has only been inspected on one Windows machine, and no real model download was made.

## Usage

`detect` finds an install, `Downloader::fetch` fetches one recipe file, `extract_7z` unpacks the pinned portable build. `examples/detect.rs` prints what it finds on this machine.
