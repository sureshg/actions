# 🅺 Kotlin Toolchain Cache

A composite GitHub Action that caches what the
[Kotlin Toolchain](https://kotlin-toolchain.org/) downloads and builds, so CI stops re-provisioning JDKs/toolchains and
re-resolving dependencies on every run. It wraps five `actions/cache` steps into one:

| Cache               | Path                                                       | Invalidated by                               |
|---------------------|------------------------------------------------------------|----------------------------------------------|
| Bootstrap           | `<kotlin-cache-dir>/boot-cache`                            | `kotlin`, `kotlin.bat`                       |
| Toolchains & JDKs   | `<kotlin-cache-dir>/shared-cache/{download,extract}.cache` | project/module/plugin yaml, wrapper, catalog |
| Maven & Kotlin deps | `<kotlin-cache-dir>/shared-cache/{.m2.cache,kotlin}`       | project/module/plugin yaml, wrapper, catalog |
| Build outputs       | `build/*` (excl. `build/logs`)                             | project/module/plugin yaml, wrapper, catalog |
| Kotlin/Native       | `~/.konan` *(opt-in)*                                      | Kotlin version (from `libs.versions.toml`)   |

Everything lives under a single `kotlin-cache-dir` (default
`${{ github.workspace }}/.kotlin`). It also exports
`KOTLIN_CLI_BOOTSTRAP_CACHE_DIR` and `KOTLIN_SHARED_CACHE_DIR` to `GITHUB_ENV` by default, so your `./kotlin` steps pick
up the cached dirs without an extra `env:` block.

## Usage

```yaml
steps:
  - uses: actions/checkout@v6

  - name: 🅺 Cache Kotlin Toolchain
    uses: sureshg/actions/kotlin-toolchain-cache@v1
    # with:
    #   konan: true   # enable ~/.konan for Multiplatform / Native

  - run: ./kotlin test
```

### Inputs

| Input                 | Default                           | Description                                |
|-----------------------|-----------------------------------|--------------------------------------------|
| `kotlin-cache-dir`    | `${{ github.workspace }}/.kotlin` | Kotlin Toolchain Cache directory.          |
| `konan`               | `false`                           | Cache `~/.konan` for Multiplatform/Native. |
| `cache-build-outputs` | `true`                            | Cache `build/` (excl. `build/logs`).       |
| `export-env`          | `true`                            | Export cache dirs to `GITHUB_ENV`.         |

**Outputs:** `bootstrap-cache-hit`, `downloads-cache-hit`, `dependencies-cache-hit`,
`build-cache-hit`, `konan-cache-hit`. Each is `'true'` on an exact cache match.

## Publishing

This folder is the source copy. Publish it so any repo can reference it:

1. Copy it to the repo root of a dedicated actions repo, e.g.
   `sureshg/actions/kotlin-toolchain-cache/`.
2. Tag a moving major version:

   ```sh
   git tag -fa v1 -m "kotlin-toolchain-cache v1" && git push origin v1 --force
   ```

3. Reference it as `sureshg/actions/kotlin-toolchain-cache@v1` (or pin `@v1.0.0` / a commit SHA for reproducibility).
   The consuming repo's `.github/actions/` copy can then be deleted.
