<p align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/postshor/.github/main/brand/logo/postshor-lockup-on-dark.png">
    <img src="https://raw.githubusercontent.com/postshor/.github/main/brand/logo/postshor-lockup-on-light.png" alt="PostShor" width="300">
  </picture>
</p>

# PostShor GitHub Action

Find quantum-vulnerable cryptography in your repository on every push and pull request.

[PostShor](https://github.com/postshor/postshor) scans source code (10 languages), dependencies, certificates, keys, server configuration and infrastructure-as-code for RSA, elliptic-curve and Diffie-Hellman cryptography, the algorithms a quantum computer running Shor's algorithm will break. Findings appear in **GitHub code scanning**, and the job summary shows a **Quantum Readiness Score** and a migration plan.

## Usage

```yaml
name: Quantum readiness
on: [push, pull_request]

permissions:
  contents: read
  security-events: write   # to upload results to code scanning

jobs:
  postshor:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: postshor/postshor-action@v1
        with:
          fail-on: critical   # block new harvest-now-decrypt-later exposure
```

## Inputs

| Input | Default | Description |
|---|---|---|
| `path` | `.` | Directory to scan |
| `fail-on` | *(empty)* | Fail if any finding is at or above `critical`, `high`, `medium` or `low`. Empty never fails. |
| `upload-sarif` | `true` | Upload results to GitHub code scanning |
| `version` | `0.1.0` | PostShor version installed from PyPI |
| `args` | *(empty)* | Extra arguments for `postshor scan`, e.g. `--exclude tests/fixtures --baseline .postshor-baseline.json` |

## Outputs

| Output | Description |
|---|---|
| `grade` | Quantum Readiness Score grade, A–F |
| `score` | Quantum Readiness Score, 0–100 |

The full HTML report, JSON results and SARIF file are uploaded as the `postshor-report` workflow artifact.

## Adopting on an existing codebase

Record today's findings once and fail only on new ones:

```
pip install postshor
postshor baseline .          # writes .postshor-baseline.json; commit it
```

```yaml
      - uses: postshor/postshor-action@v1
        with:
          fail-on: high
          args: --baseline .postshor-baseline.json
```

## Links

- Scanner, rules and documentation: [postshor/postshor](https://github.com/postshor/postshor)
- [Quantum Readiness Score specification](https://github.com/postshor/postshor/blob/main/docs/spec/qrs-v1.md)
- Issues and feature requests: [postshor/postshor/issues](https://github.com/postshor/postshor/issues)

Licensed under the [Apache License 2.0](LICENSE).
