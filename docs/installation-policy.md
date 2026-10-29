# Installing the Craft apps on Windows

This is a proposed policy, not a claim about a published release. Preparation covers every
current Craft app with a Windows MSI, plus the launcher's new-install folder. The
[adoption inventory](windows-installation-adoption.md) records the scope and remaining gates.
Only a copy in the old default folder moves (see below); chosen folders never do. Storage of settings, caches and models is a
separate policy: [settings-storage.md](settings-storage.md).

## Installation contexts

One dual-scope MSI per app (Microsoft's "Single Package Authoring": `ALLUSERS=2` with
`MSIINSTALLPERUSER=1` in the Property table, written by WiX's `Package/Scope="perUserOrMachine"`):

| Context | Default destination | How it is chosen |
|---|---|---|
| Per-machine (the default for new installs) | `%ProgramFiles%\ArtCraft\<App>` (`Program Files (x86)` for a 32-bit package) | The launcher installs with `ALLUSERS=1`; the setup wizard's "Everyone" choice |
| Per-user (kept for existing copies) | `%LOCALAPPDATA%\Programs\ArtCraft\<App>` | Copies installed per-user keep their context on update; the wizard's "Only for me" choice |

Amended 29/10/2026: new installs default to per-machine, so every app sits in one
`Program Files\ArtCraft` folder. **Update all** asks Windows for permission once per run,
through a single elevated helper that closes when the run ends, not a resident service.

The launcher itself remains per-user only, with new copies under
`%LOCALAPPDATA%\Programs\ArtCraft\Launcher`. Existing launcher folders and its current
self-update mechanism are preserved.

In a per-user install Windows Installer itself redirects the `ProgramFiles` folder to the
user's own Programs folder and the `HKMU` registry root to `HKCU`, so one package serves both
contexts. Everything a package writes follows its context automatically:

- `InstallDir` record: `HKLM\Software\<App>` per-machine, `HKCU\Software\<App>` per-user.
- File associations and "Open with" registrations: `HKLM\Software\Classes` per-machine,
  `HKCU\Software\Classes` per-user.
- Registry writes use context-sensitive roots. Existing component key paths and app-owned
  associations are retained where valid. Each package must pass the compiled install-policy
  checks; unresolved ICE57 shortcut diagnostics are tracked separately from the path policy.

Scope is chosen before launch conditions and directory costing. The scope page must not be
added as a late checkbox after the default directory has already been resolved.
The page also has to leave `ALLUSERS` unset for "Only for me". Setting `ALLUSERS=2` there makes
Windows Installer search for related products as if the run were per-machine, so an interactive
upgrade skips the existing per-user copy and installs a second registration beside it.
Windows Installer also resolves Program Files once, for the context it started in, so the wizard
sets the new-install folder itself after the choice. Without that, "Everyone" installed into the
user profile with a machine-wide registration.

The ArtCraft Launcher and a direct install of the same MSI produce the same result: the
launcher dispatches the same package and only adds `INSTALLFOLDER` (the user's chosen folder)
and the context properties of the copy being updated.

## What an update must preserve

- **The exact folder.** An ordinary update installs into the remembered folder of the copy
  being replaced, wherever the user put it. A folder someone chose is never moved.
- **One exception: the old default.** A copy whose remembered folder is exactly the
  pre-suite default `%ProgramFiles%\<App>\` moves to `%ProgramFiles%\ArtCraft\<App>` on its
  next update. A folder that cannot be read counts as chosen and stays. Someone who typed
  `C:\Program Files\<App>` by hand looks the same as the old default and will be moved;
  Windows Installer kept no record of whether that folder was chosen.
- **The context.** A per-machine copy updates per-machine (`ALLUSERS=1`), a per-user copy
  per-user. MSI major upgrades cannot cross installation contexts, so a context change is a
  deliberate uninstall-and-reinstall, never an ordinary upgrade. The launcher queries
  authoritative MSI registration, not the hive containing a display entry. An unknown or
  conflicting context stops the update rather than selecting the new-install default.
- **No duplicates.** Installing in the other context while a copy already exists is a visible
  stop with a message naming the fix, never a silent second copy. The wizard pre-ticks the
  matching scope when a copy in the other context is registered.
- **The chosen folder survives elevation.** `INSTALLFOLDER` is a secure MSI property, so
  the elevated installer receives the same exact destination selected in the setup wizard.
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
models live and what is disposable. Model resources shipped inside an application package,
such as bundled OCR resources, remain part of that package; this does not give an installer
ownership of separately downloaded shared models or discovered model folders.
