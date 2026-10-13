# Shared AI layer: what lives where

The shared AI layer for the Craft Apps: one connection, set up once, used by every app that has the
tool. It owns connections, models, the tool to model binding, the picker data, consent and jobs. It
does not own any app's interface. Each app decides how a tool looks (a prompt bar, a one-click
button, a click on the image), in its own repository. The contract is in
[ai-shared-layer.md](ai-shared-layer.md).

## Crates

| Group | Crates | Owns |
|---|---|---|
| Core | `ai-model`, `ai-native`, `ai-client`, `ai-features` | The data model and provider trait (wasm-safe), secret stores, the shared store and its location, history and consent files, the app-facing client and tool desk, the tool catalogue and setup options. |
| Adapters | `ai-openai`, `ai-bfl`, `ai-vertex`, `ai-artcraft`, `ai-seedance`, `ai-comfyui`, `ai-sam3` | One crate per provider API shape. Each is registered in `ai-client`'s `ProviderFactory`. |
| Local runtime | `ai-comfy-env`, `ai-media` | Finding an existing ComfyUI, model sources and checksummed downloads, node packs, and durable media transfer. |
| Widgets | `ai-egui` | Theme-neutral egui widgets (status, consent, progress, connection panel, history, model picker) for apps that have no chrome of their own. |


Apps depend on a crate by git revision, as for every craft-libs crate. `craft-ai-model` has no
dependencies; the adapters, `craft-ai-native` and `craft-ai-media` depend on it; `craft-ai-client`
depends on every adapter; `craft-ai-egui` depends on the client.

## Not in craft-libs

| What | Where it goes |
|---|---|
| PhotoCraft's Select subject panel, Generate bar, `ai_cmds`, hover session | `storytold/photocraft`, through its own PR and its maintainers |
| The launcher's Set up AI window, History page and ports | `storytold/craft-launcher` |
| LightCraft's, EffectCraft's and any other app's adapter | That app's repository |
| Anything that draws a toolbar or prompt bar | The app. The shared crates never do. |

The shared code knows an app only as an `AppId` string and as data in the catalogue's `uses`
list. If a change here needs to name an app's behaviour, it belongs in the app.

## How an app uses this

1. Declare a role with the tool's id (the catalogue's `Feature::role()`), for example
   `select-subject`. Do not invent an id of your own.
2. Call `AiClient::status` to grey the tool out, or to say that a model is needed. Never report a
   tool with no model as broken.
3. Depend on a pinned git revision, not a path.
4. A person's choice made for the whole suite is `ToolDesk::choose` (the default every app reads).
   `AiClient::bind` is that one app's own override.

## How an app uses this

1. Declare a role with the tool's id (the catalogue's `Feature::role()`), for example
   `select-subject`. Do not invent an id of your own.
2. Call `AiClient::status` to grey the tool out, or to say that a model is needed. Never report a
   tool with no model as broken.
3. Depend on a pinned git revision, not a path.
4. A person's choice made for the whole suite is `ToolDesk::choose` (the default every app reads).
   `AiClient::bind` is that one app's own override.

