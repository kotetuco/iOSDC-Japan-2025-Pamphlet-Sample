# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

Sample project for an iOSDC Japan 2025 pamphlet article. The repository demonstrates the same article produced in three different DTP tools, sharing a common set of image assets.

## Repository Structure

Three parallel DTP implementations of the same article:

- **`InDesign/`** — Adobe InDesign source (`.indd`) and PDF output. The `.idml` export in `AffinityPublisher/` was generated from this file for cross-tool conversion.
- **`AffinityPublisher/`** — Affinity Publisher source (`.afpub`) and PDF output. Also contains the InDesign template (`.idml`) used as a starting point when importing from InDesign.
- **`ReVIEW/`** — Re:VIEW-based version. Only `review/book.pdf` is tracked; source `.re` files and config are untracked and stored locally. Re:VIEW is a Ruby-based markup language and toolchain used for technical book publishing in Japan.

- **`images/`** — Shared image assets (figures, QR code, title image) referenced by all three DTP projects.

## Key File Formats

| Extension | Tool |
|---|---|
| `.indd` | Adobe InDesign source |
| `.afpub` | Affinity Publisher source |
| `.idml` | InDesign Markup Language (interchange format) |
| `.re` | Re:VIEW markup source (not tracked) |

## Re:VIEW Notes

Re:VIEW is typically built with Docker or a local Ruby environment. Standard commands when source files are present:

```sh
# Build PDF via Docker (common setup)
docker run --rm -v $(pwd):/work vvakame/review:latest /bin/bash -c "cd /work && review-pdfmaker config.yml"

# Or with local Re:VIEW gem
review-pdfmaker config.yml
```

The `.gitignore` is Ruby-based, reflecting Re:VIEW's Ruby toolchain.
