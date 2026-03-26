
---

### `skills/setup-dog-database/SKILL.md`

```markdown
---
name: setup-dog-database
description: Set up the local "dog database" workspace by downloading dog_database.csv and Dogs_Images.zip from the known GCS URLs, verifying them, and unpacking the images for further processing. Use when you need the dog CSV and images available locally.
---

# Setup Dog Database

This skill standardizes how to fetch and prepare the dog database (CSV + images) into the current workspace.

## Source URLs

These are the canonical public URLs for the dataset:

- CSV: `https://storage.googleapis.com/openclaw-nacho/dog_database.csv`
- Images ZIP: `https://storage.googleapis.com/openclaw-nacho/Dogs_Images.zip`

If these URLs change, update them here and in any helper scripts.

## Expected Layout

After running the setup, the workspace should contain:

- `dog_database.csv` — main data table
- `Dogs_Images.zip` — archive of all images
- `Dogs_Images/` — unzipped folder of images (e.g. `Dogs_Images/Helmo.jpg`, `Dogs_Images/Pistache.jpg`, ...)

## Quick Setup (Bash)

### Option A — One-liner script

From any directory (script assumes workspace at `/data/.openclaw/workspace`):

```bash
bash skills/setup-dog-database/scripts/setup_dog_database.sh
```

### Option B — Manual commands

From the workspace root (where AGENTS.md lives), run:

```bash
cd /data/.openclaw/workspace

# 1) Download CSV
curl -L -o dog_database.csv \
  "https://storage.googleapis.com/openclaw-nacho/dog_database.csv"

# 2) Download images ZIP
curl -L -o Dogs_Images.zip \
  "https://storage.googleapis.com/openclaw-nacho/Dogs_Images.zip"

# 3) Unzip images into Dogs_Images/
unzip -q Dogs_Images.zip -d .
```

Notes:
- `-L` follows redirects if any.
- The ZIP already contains a `Dogs_Images/` directory; extracting to `.` is expected.

## Verification Steps

After setup, verify:

```bash
cd /data/.openclaw/workspace

# CSV exists and has a header
head -n 5 dog_database.csv

# ZIP exists and basic listing works
unzip -l Dogs_Images.zip | head

# Images directory exists and contains sample files
ls Dogs_Images | head

# Spot-check specific known files
ls Dogs_Images/Helmo.jpg
ls Dogs_Images/Pistache.jpg
```

## Usage Patterns

Use this skill when:

- You need to run any analysis that joins dog records to image files.
- You want to script operations that rely on `dog_database.csv` and `Dogs_Images/` being present.
- You are re-creating the environment on a fresh machine or fresh workspace.

## Regeneration / Refresh

To refresh to the latest version of the dataset from the canonical URLs, simply re-run the download commands. If you suspect partial downloads, delete the existing files first:

```bash
cd /data/.openclaw/workspace
rm -f dog_database.csv Dogs_Images.zip
rm -rf Dogs_Images

# Then run the Quick Setup commands again.
```

If the dataset is versioned in the future (e.g., URLs change or timestamps are embedded), update the URLs here and any automation scripts that depend on them.
```

---

### `skills/setup-dog-database/scripts/setup_dog_database.sh`

```bash
#!/usr/bin/env bash
set -euo pipefail

# Setup script for the dog database: CSV + images
# Usage: run from any directory
#   ./setup_dog_database.sh
# or
#   bash setup_dog_database.sh

WORKSPACE_DIR="/data/.openclaw/workspace"
CSV_URL="https://storage.googleapis.com/openclaw-nacho/dog_database.csv"
IMAGES_ZIP_URL="https://storage.googleapis.com/openclaw-nacho/Dogs_Images.zip"

cd "$WORKSPACE_DIR"

echo "[dog-db] Downloading CSV..."
curl -L -o dog_database.csv "$CSV_URL"

echo "[dog-db] Downloading images ZIP..."
curl -L -o Dogs_Images.zip "$IMAGES_ZIP_URL"

echo "[dog-db] Unzipping images..."
unzip -q Dogs_Images.zip -d .

echo "[dog-db] Done. Files available at:"
echo "  - $WORKSPACE_DIR/dog_database.csv"
echo "  - $WORKSPACE_DIR/Dogs_Images/"
```

