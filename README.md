# Spack Build-Cache Action

![Version][version-badge] ![License][license-badge]

[version-badge]: https://img.shields.io/badge/version-v1.0.0-blue.svg  
[license-badge]: https://img.shields.io/badge/license-MIT-blue.svg  
[spack]: https://spack.io  

A GitHub Actions composite action to:

1. Install your project (and dependencies) via [Spack][spack]  
2. Push the resulting binary buildcache to a remote OCI registry mirror

---

## Features

- Checks out your repository (including submodules)  
- Sets up Spack via `spack/setup-spack`  
- Clones your custom Spack package recipes repo  
- Adds it as a Spack repository  
- Installs the specified Spack environment variant  
- Pushes the buildcache to a specified OCI mirror  

---

## Usage

Add this step to your workflow:

```yaml
jobs:
  build-cache:
    runs-on: ubuntu-24.04
    permissions:
      packages: write

    steps:
      - name: Build & Push Spack Buildcache
        uses: numpex/spack-buildcache-action@v1
        with:
          env-variant: default
          mirror: my-org/my-spack-mirror
          # Optional inputs shown with their defaults:
          # repo-packages: numpex/spack.numpex
          # repo-packages-path: spack.numpex
          # spack-path: _spack
          # base-image: ubuntu:24.04
```

---

## Inputs

| Input              | Description                                                                 | Required | Default               |
| ------------------ | --------------------------------------------------------------------------- | :------: | --------------------- |
| `env-variant`      | Spack environment variant to install (e.g. `default`, `omp`, etc.)         |   ✅     | —                     |
| `mirror`           | Name of the OCI mirror (e.g. `my-org/my-spack-mirror`)                     |   ✅     | —                     |
| `repo-packages`    | GitHub repo with your Spack package recipes                                 |          | `numpex/spack.numpex` |
| `repo-packages-path` | Local directory to clone the Spack packages repo                          |          | `spack.numpex`        |
| `spack-path`       | Local install path for the Spack checkout                                   |          | `_spack`              |
| `base-image`       | Base Docker image passed to `spack buildcache push --base-image`            |          | `ubuntu:24.04`        |

---

## Example Workflow

```yaml
name: CI

on:
  push:
    tags:
      - 'v*'

jobs:
  build-cache:
    runs-on: ubuntu-24.04
    permissions:
      packages: write

    steps:
      - name: Build & publish Spack cache
        uses: numpex/spack-buildcache-action@v1
        with:
          env-variant: default
          mirror: numpex/spack-buildcache
```

---

## Test Workflow Example

Here’s a sample workflow you can use within your Spack Build-Cache Action repository to test the action locally:

```yaml
name: Test Spack Build-Cache Action

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  smoke:
    runs-on: ubuntu-24.04
    permissions:
      contents: read
      packages: write

    steps:
      # 1) Check out this action’s code
      - name: Checkout action
        uses: actions/checkout@v4

      # 2) Run our composite action locally
      - name: Run Spack Build-Cache
        uses: ./
        with:
          env-variant: default
          repo-packages: numpex/spack.numpex
          repo-packages-path: spack.numpex
          mirror: numpex-buildcache

      # 3) Verify installed specs
      - name: List installed specs
        run: |
          . _spack/share/spack/setup-env.sh
          spack find -lv
```

---

## License

[BSD-3 License](LICENSE)
