# GitHub Actions

Reusable GitHub Actions for [Kotlin Toolchain](https://kotlin-toolchain.org/) projects.

## Actions

| Action | Description |
|---|---|
| [`kotlin-toolchain-cache`](kotlin-toolchain-cache) | Caches the Kotlin Toolchain bootstrap, JDKs/toolchains, dependencies, build outputs and (opt-in) `~/.konan`. |

### Usage

```yaml
steps:
  - uses: actions/checkout@v6

  - name: 🅺 Cache Kotlin Toolchain
    uses: sureshg/actions/kotlin-toolchain-cache@v1

  - run: ./kotlin test
```

See each action's README for inputs and outputs.
