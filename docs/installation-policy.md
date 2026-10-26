# Installing the Craft apps on Windows

Where the Craft apps install, which registrations they write, and what an update may and may
not change. The pilot implementation is PhotoCraft and the ArtCraft Launcher; other apps adopt
this policy one at a time, and nothing here moves an existing installation. Storage of
settings, caches and models is a separate policy: [settings-storage.md](settings-storage.md).

## Installation contexts

One dual-scope MSI per app (Microsoft's "Single Package Authoring": `ALLUSERS=2` with
`MSIINSTALLPERUSER=1` in the Property table, written by WiX's `Package/Scope="perUserOrMachine"`):

| Context | Default destination | How it is chosen |
|---|---|---|
| Per-user (the default) | `%LOCALAPPDATA%\Programs\ArtCraft\<App>` | Just install: no UAC prompt |
| Per-machine (an explicit choice) | `%ProgramFiles%\ArtCraft\<App>` (per package architecture) | The setup wizard's "Install for all users" box, or `msiexec ... ALLUSERS=1` |

In a per-user install Windows Installer itself redirects the `ProgramFiles` folder to the
user's own Programs folder and the `HKMU` registry root to `HKCU`, so one package serves both
contexts. Everything a package writes follows its context automatically:

- `InstallDir` record: `HKLM\Software\<App>` per-machine, `HKCU\Software\<App>` per-user.
- File associations and "Open with" registrations: `HKLM\Software\Classes` per-machine,
  `HKCU\Software\Classes` per-user.
- Components are keyed on registry values, never on files, so they are valid in both
  contexts (ICE38).

The ArtCraft Launcher and a direct install of the same MSI produce the same result: the
launcher dispatches the same package and only adds `INSTALLFOLDER` (the user's chosen folder)
and the context properties of the copy being updated.

## What an update must preserve

- **The exact folder.** An ordinary update installs into the remembered folder of the copy
  being replaced, wherever the user put it. A later change to the suite default moves nothing.
- **The context.** A per-machine copy updates per-machine (`ALLUSERS=1`), a per-user copy
  per-user. MSI major upgrades cannot cross installation contexts, so a context change is a
  deliberate uninstall-and-reinstall, never an ordinary upgrade.
- **No duplicates.** Installing in the other context while a copy already exists is a visible
  stop with a message naming the fix, never a silent second copy. The wizard pre-ticks the
  matching scope when a copy in the other context is registered.
- **Custom folders stay first-class.** A chosen exact app folder is used as-is; a chosen
  suite root gets the app subfolder appended once (`D:\Craft` becomes `D:\Craft\PhotoCraft`,
  never `D:\Craft\PhotoCraft\PhotoCraft`). A missing drive or an unavailable folder is a
  visible error, not permission to fall back to the default.

## What an app must still do without the launcher

Install, launch, update and uninstall work standalone: the MSI's own wizard, its App Paths
registration (`Win+R`), its Start Menu shortcut and its entry in "Apps & features" never
require the launcher. The launcher reuses a standalone installation it finds rather than
installing a duplicate, and detection reads both contexts' registrations.

## Other platforms

macOS `.app` bundles, Linux packages/AppImages and FreeBSD tarballs keep their platform-native
conventions; nothing imitates the Windows directory tree. Linux settings live under
`XDG_CONFIG_HOME` and persistent models under `XDG_DATA_HOME` (`~/.local/share/ArtCraft/models`)
per [settings-storage.md](settings-storage.md).

## Models are not caches

Downloaded model files are persistent user assets. An app's cache cleanup and an app's
uninstaller must never delete them, another app's data, or user documents; existing model
folders are discovered read-only. See [settings-storage.md](settings-storage.md) for where
models live and what is disposable.