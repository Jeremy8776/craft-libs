# Craft AI media

`craft-ai-media` keeps durable local copies of media that a hosted AI provider returns as a link.
A provider's link is untrusted input: it is followed only over `https` (plain `http` only for a
loopback host the caller allows), with no user-info, to no private or internal address (including
IPv6 forms that carry an IPv4 address, and names that resolve to one), and the bytes are kept only
if they look like the media the provider declared.

Native only. The app owns where the files go and when they are deleted.

`craft-ai-test-server` (dev-dependency) is a scripted localhost HTTP server for tests.

## Platforms

Windows, macOS and Linux (native only). Run in CI on all three against scripted localhost servers.

## Usage

`MediaStore::new(MediaStoreConfig::new(dir))?.save(&handle)` downloads a provider's link into `dir` and returns a `file:` handle; refused links and failed transfers leave no file. See `tests/persist.rs`.
