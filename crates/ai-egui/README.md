# craft-ai-egui

Shared egui widgets for the Craft AI layer: connection status and model picker, consent prompt, job progress with cancel..

Part of the shared AI layer for the Craft Apps; the contract is in [docs/ai-shared-layer.md](../../docs/ai-shared-layer.md) and the data model in [`craft-ai-model`](../ai-model/README.md).

## Boundaries

- Never crashes: the crate denies `unwrap`, `expect`, `panic` and friends, and the workspace forbids `unsafe`.
- A secret value never reaches `Debug`, `Display`, an error message or a log line.
- No app code: an app is only an `AppId` string.

Status and model picker, consent prompt, job progress with cancel, the connection panel and request history, as widgets for apps that have no chrome of their own. They draw nothing an app's theme has not already set. Needs Rust 1.95 (egui 0.36).

## Platforms

Desktop (Windows, macOS, Linux) with egui 0.36; Rust 1.95. Tests run headless through `egui_kittest`; nothing was tried under a real app theme.

## Usage

Call the widgets from an `egui::Ui` with an `AiClient` handle; `examples/picker_demo.rs` shows the picker and `tests/panel.rs` the connection panel.
