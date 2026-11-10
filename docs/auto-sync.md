# Automated upstream synchronization and prereleases

The mirror follows **[peiikai/LASRecomp](https://github.com/peiikai/LASRecomp)**. It checks both `main` (stable) and `beta_6` (development) every four hours using cron `47 */4 * * *` UTC. Cron scheduling is best-effort.

- Source branches are mirrored to matching branch names. New commits are merged, never force pushed. On conflict the job fails without overwriting local changes.
- Local `.github/workflows` files are preserved during upstream merges because the built-in GitHub Actions token cannot import modified workflows from upstream.
- A unique snapshot tag `ci-test-sync-<branch>-<upstream SHA prefix>` is associated with each upstream commit. Duplicate checks do not create another release.
- A new upstream commit dispatches the existing Release workflow for the exact merged commit with `mirror_snapshot=true`.
- The mirror's snapshot mode skips the upstream's private self-hosted macOS runner, but requires the Windows x64 and Linux x86_64 / aarch64 jobs to succeed.
- After the required binaries are packaged, the Release workflow publishes a **prerelease** (`latest=false`), with SHA-256 hashes; it never marks the snapshot as the stable release.
- Original manual release workflows are unchanged unless `mirror_snapshot` is explicitly set. A failure does not count as a release.

Initial installation also requests one development (`beta_6`) baseline build; the stable `main` branch is only built when its upstream changes, or when an operator manually requests a sync.

For a retry, use **Actions > Sync upstream and publish > Run workflow**. If a release already exists, it is not rebuilt. Check **Actions > Release** and **Releases** for exact results.

No game images, keys, extracted files, generated game code or private assets are included in the released packages. Players must supply their own supported game files.
