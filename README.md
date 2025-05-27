# Spack Build-Cache Action


![GitHub Release](https://img.shields.io/github/v/release/numpex/spack-buildcache-action)
![GitHub License](https://img.shields.io/github/license/numpex/spack-buildcache-action)
![Static Badge](https://img.shields.io/badge/packaging-spack-blue)

[spack]: https://spack.io

A GitHub Actions composite action to build and push a Spack environment into an OCI registry mirror.


## Usage

Add the step after your `spack/setup-spack` action:

```yaml
jobs:
  build-cache:
    runs-on: ubuntu-24.04
    permissions:
      packages: write

    steps:
      - name: Set up Spack
        uses: spack/setup-spack@v2

      - name: Build and push Spack environment
        uses: numpex/spack-buildcache-action@v2
        with:
          environment: .spack/default

      - name: Build app
        # Use your Spack packages automatically
        run: |
          cmake -B build
          cmake --build build
```


## Inputs

| Input              | Description                                                                     | Required | Default                                  |
| ------------------ | ------------------------------------------------------------------------------- | :------: | ---------------------------------------- |
| `environment`      | Relative path to the Spack environment (e.g. `.`).                              |    ✅     |                                          |
| `load-environment` | Whether the Spack environment should be loaded to subsequent steps.             |          | `true`                                   |
| `mirror`           | OCI registry to push the packages to.                                           |          | `oci://ghcr.io/${{ github.repository }}` |
| `token`            | OCI token to use for pushing the packages.                                      |          | `${{ github.token }}`                    |
| `base-image`       | Image name to attach to the packages, to use them as standalone containers.     |          |                                          |
| `force`            | Force push packages into the registry.                                          |          | `false`                                  |



## License

[BSD-3 License](LICENSE)
