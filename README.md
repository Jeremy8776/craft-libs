Craft App Libraries
===================

This repo contains libraries used by the various Craft Apps. It's a Rust monorepo workspace, and the libraries can be included by any Craft Apps that need them.

In the future this may grow to include non-Rust library code.

If any libraries grow beyond Craft App usage, eg. RAW format support, they may be promoted out and into their own libraries for the broader ecosystem to take advantage of.


## Layout

A Cargo workspace (edition 2024, resolver 3). Each library is a crate under `crates/<name>`, named
`craft-<name>`, with `version`, `edition`, `license` and `repository` inherited from the workspace
(`license.workspace = true`, and so on) and `[lints] workspace = true`. Register each crate in
`[workspace.dependencies]` in the root `Cargo.toml`.

Apps depend on a crate by git:

```toml
craft-<name> = { git = "https://github.com/storytold/craft-libs", rev = "<commit>" }
```

## Libraries

- [`craft-ai-model`](crates/ai-model/README.md): the data model and contract for AI in the Craft Apps (connections, models, tools, requests, jobs, consent, history, route spec). Pure, no I/O, wasm-safe. Contract in [docs/ai-shared-layer.md](docs/ai-shared-layer.md).
- [`craft-ai-native`](crates/ai-native/README.md): the desktop half of the shared AI layer: secret stores (OS keychain, encrypted file, environment), the shared connections file, consent, request history and catalog files.
- [`craft-ai-media`](crates/ai-media/README.md): durable local copies of media a hosted AI provider returns as a link, with strict link rules.
- [`craft-ai-openai`](crates/ai-openai/README.md): the OpenAI-compatible provider adapter (text, tools, structured output, image generate, edit and inpaint).
- [`craft-ai-bfl`](crates/ai-bfl/README.md), [`craft-ai-vertex`](crates/ai-vertex/README.md), [`craft-ai-artcraft`](crates/ai-artcraft/README.md), [`craft-ai-seedance`](crates/ai-seedance/README.md), [`craft-ai-sam3`](crates/ai-sam3/README.md): small provider adapters for the shared AI layer (Black Forest Labs, Google Vertex, ArtCraft's API, Seedance through BytePlus, a SAM 3 bridge).
- [`craft-ai-features`](crates/ai-features/README.md), [`craft-ai-declarative`](crates/ai-declarative/README.md), [`craft-ai-comfyui`](crates/ai-comfyui/README.md): the AI tool catalogue and model recipes; the runner for a route described as data; the local ComfyUI adapter (workflows, SAM 3).
- [`craft-atomic`](crates/atomic/README.md): Crash-safe atomic file writing and replacement with same-directory staging, directory fsync, Apple `F_FULLFSYNC` fallback, transient lock backoff, and fault injection for the Craft suite.
- [`craft-cms`](crates/cms/README.md): pure-Rust ICC v2/v4 colour management: profile parsing
  and writing, transforms, rendering intents, black point compensation, soft proofing, gamut
  checks, 3D LUTs and built-in profiles (sRGB, Display P3, synthetic coated CMYK and others).
- [`craft-print`](crates/print/README.md): PDF submission to CUPS and Windows, printer discovery,
  duplex/copies/colour and driver preferences. PDF generation stays in each app.

- [`craft-quit`](crates/quit/src/lib.rs): native macOS quit, egui close handling and an optional
  app menu. Unsaved-document decisions and save prompts stay in each app.

- [`craft-settings`](crates/settings/README.md): `config.toml` settings storage for every app: platform folders, portable mode and overrides, atomic saves while running, backup, damaged-file recovery and schema migrations. Policy in [docs/settings-storage.md](docs/settings-storage.md).
- [`craft-ui`](crates/ui/README.md): SVG catalogs, icon buttons, tabs, pane controls, transactional docking, print settings and theme helpers for egui frontends;
  assets, themes, localization and command handling stay in each app. Requires Rust 1.95 or later.
- [Shared UI component audit](docs/ui-components-audit.md): source inventory, extraction priorities
  and compatibility boundaries across the Craft suite.
- [Theme infrastructure](docs/theme-infrastructure.md): eight-app comparison and the small shared
  widget-state styling boundary.
- [Panel docking](docs/ui-docking.md): validated panel moves, floating groups, workspace adapters
  and an optional egui docking surface.

## License and credits

craft-libs is dual-licensed under [MIT](LICENSE-MIT) or [Apache-2.0](LICENSE-APACHE), at your option.
Copyright (c) 2026 ArtCraft Team and the craft-libs contributors. Required notices are in [NOTICE](NOTICE).

Bundled fonts, icons, images and other assets keep their own open licenses; each one is listed
with its author, source and license in [ATTRIBUTION.md](ATTRIBUTION.md).
