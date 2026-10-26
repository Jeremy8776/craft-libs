# Windows installation policy adoption

This is the source-preparation inventory for the proposed policy. None of these local changes
has been published, accepted or released. The inventory covers the live app repositories and
launcher, not merely the repositories on one developer's machine.

| Repository | Prepared Windows scope | Verification boundary |
|---|---|---|
| `effectcraft` | Per-user default; explicit all-users option | x64, x86 and arm64 metadata checks pass; real installation gates pending |
| `pdfcraft` | Per-user default; explicit all-users option | x64, x86 and arm64 metadata checks pass; real installation gates pending |
| `lightcraft` | Per-user default; explicit all-users option | x64, x86 and arm64 metadata checks pass; real installation gates pending |
| `photocraft` | Per-user default; explicit all-users option | x64, x86 and arm64 metadata checks pass; real installation gates pending |
| `figcraft` | Per-user default; explicit all-users option | x64, x86 and arm64 metadata checks pass; real installation gates pending |
| `vectorcraft` | Per-user default; explicit all-users option | x64, x86 and arm64 metadata checks pass; real installation gates pending |
| `wordcraft` | Per-user default; explicit all-users option | x64, x86 and arm64 metadata checks pass; real installation gates pending |
| `gridcraft` | Per-user default; explicit all-users option | x64, x86 and arm64 metadata checks pass; real installation gates pending |
| `deckcraft` | Per-user default; explicit all-users option | x64, x86 and arm64 metadata checks pass; real installation gates pending |
| `filmcraft` | Per-user default; explicit all-users option | x64, x86 and arm64 metadata checks pass; real installation gates pending |
| `craft-launcher` | Per-user launcher; context-safe app updates | x64, x86 and arm64 metadata checks pass; real installation gates pending |
| `artcraft` | Not applicable | No Windows MSI definition on the inventoried default branch |
| `soundcraft` | Per-user default; explicit all-users option | x64, x86 and arm64 metadata checks pass; real installation gates pending |
| `cadcraft` | Per-user default; explicit all-users option | x64, x86 and arm64 metadata checks pass; real installation gates pending |
| `designcraft` | Per-user default; explicit all-users option | x64, x86 and arm64 metadata checks pass; real installation gates pending |
| `artcraftx` | Not applicable | No Windows MSI definition on the inventoried default branch |
| `AniCraft` | Not applicable | No Windows MSI definition on the inventoried default branch |

## Before publication and release

- Maintainer acceptance of the shared path/scope/model-ownership policy is still required.
- Keep app changes independent. Reconcile open installer UI and file-association PRs rather
  than assuming their work is absent or overwriting it.
- Test real release payloads and previous release MSIs in an authorised clean Windows test
  environment. Verify both standalone and launcher routes, old/custom folders, both scopes,
  second-account visibility, optional shortcuts and menus, active apps, cancellation, rollback,
  uninstall and launcher self-update. Validate actual MSI assignment context, not only the
  registry hive containing a display/uninstall entry.
- Metadata-only fixtures never substitute for those tests. x86/arm64 package validation on an
  x64 host does not prove application execution on those architectures.
- No macOS/Linux layout change, settings migration, user-model relocation or new cleanup is
  included. These platforms retain their native conventions.
- ArtCraft and ArtCraftX have no Windows MSI definition in the inventoried source; AniCraft
  has no default branch/source to package. Do not invent installers for those repositories as
  part of this rollout.
