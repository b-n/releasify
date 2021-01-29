# Releasify

A GitHub action (utilising the `gh` cli) to aid in creating and releasing assets from your repo

## Usage

### Pre-requisities

This has only been tested on the `ubuntu-latest` image.

The `GITHUB_TOKEN` needs to be passed through as an `env` var.

### Inputs

- `release_name`: [string] - The name of the release to create/update
- `name`: [string] - The name of the asset to upload
- `compress`: [boolean] - Whether to run a compression command before uploading (must then provide files)
- `glob`: [string] - The glob to compress
- `working_directory` (optional): The directory to change to before compressing
- `clobber`: [boolean] - Override the existing asset, or just fail
- `create_release` - [boolean] - this will force the creation of the release if it doesn't exist

### Outputs

None

### Example workflow

```yml
on:
  push:
    tags:
      - 'docs*'

name: Release Documentation

jobs:
  build:
    name: Create and release asset
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v2
      - name: Create and Release
        uses: b-n/releasify
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        with:
          release_name: documentation
          name: 'docs.tar.gz'
          compress: true
          glob: '**/*'
          working_directory: docs
          clobber: true
          create_release: true
```

This command will:

1. Compress the glob `**/*` from the `docs` working directory (runs `tar -zcvf $OUTPUT_FILE **/*`)
2. Looks for the `documentation` release name, if it does not exist, it creates it (in draft)
3. Uploads the compressed output to the release
