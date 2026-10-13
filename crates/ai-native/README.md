# Craft AI native

`craft-ai-native` is the desktop half of the shared AI layer: secret stores, the shared connections
file, and the consent, history and catalog files. It does file and operating-system I/O, so it
must never be a dependency of a wasm build. The data model it serves is
[`craft-ai-model`](../ai-model/README.md), which stays I/O free.

| Module | What it holds |
|---|---|
| `secrets` | The `SecretStore` trait and its stores: OS keychain, passphrase-encrypted file (Argon2id, XChaCha20-Poly1305), environment, in-memory, and a layered chain |
| `shared` | `SharedStore`: the launcher's versioned connections file, read by apps, updated under a lock with an atomic write |
| `ConsentFile` | One app's consent ledger on disk. Damage makes the app ask again |
| `HistoryFile` | The shared request log (`jobs.jsonl`): locked appends, size-capped rotation, tolerant reads |
| `paths` | Where those files live on each OS |

## Boundaries

- A secret value never reaches `Debug`, `Display`, an error message, a log line or a file in the
  clear. The shared store file has no field that can hold one.
- Writes go through [`craft-atomic`](../atomic/README.md), with new files private to the user, and
  are serialised across processes by a lock file.
- Nothing panics on bad input. A truncated, oversized or newer-schema file is an error.

## Where the files live

The AI files are shared by every Craft App and written by the launcher, so they sit in one
`ArtCraft/ai` folder rather than in each app's own config folder
([docs/settings-storage.md](../../docs/settings-storage.md) covers per-app settings). On Windows
that folder is under `%LOCALAPPDATA%`, not the roaming profile: the connections list points at
keychain entries that do not exist on another machine. `CRAFT_AI_DIR` overrides it. This is a
deliberate difference from the settings policy and is open for review.

## Platforms

Windows (Credential Manager), macOS (Keychain), Linux (Secret Service over D-Bus), with an encrypted-file fallback everywhere. Native only. The suite runs in CI on all three; the real-keychain round trip is `#[ignore]`d and has not been run in CI.

## Usage

`SharedStore::at_default_location()` opens the connections file; `ConsentFile::at_default_location(&app)` and `HistoryFile::at_default_location()` open the per-app ledger and the shared log; a `LayeredStore` of `EnvStore` and `KeychainStore` resolves keys. `craft-ai-client` wires these for an app.
