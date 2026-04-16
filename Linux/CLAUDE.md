# Ramesh Knowledge System — File Uploader

This folder contains `rks-upload`, a standalone tool for uploading files to (and managing folders on) the shared Zoho WorkDrive workspace for the Ramesh Knowledge System project.

Current version: see `./VERSION` (plain text) or run `./rks-upload --version`. If behaviour here doesn't match what the binary does, that bundle is out of date — ask **Amitabh** for a fresh one.

## Quick Start

```bash
# First time only — make it executable
chmod +x ./rks-upload

# Upload all documents from a folder
./rks-upload /path/to/files

# Preview first (recommended)
./rks-upload /path/to/files --dry-run
```

## All Commands

```bash
# Upload documents (PDF, EPUB, DOCX, PPTX, XLSX, TXT, CSV, PNG, JPG)
./rks-upload /path/to/files

# Upload into a named (nested OK) subfolder on the drive
./rks-upload /path/to/files --subfolder "Chapter-3-Resources"
./rks-upload /path/to/files --subfolder "Physics/Ch3/Notes"

# Upload ALL file types, not just documents
./rks-upload /path/to/files --all-types

# Preview what would be uploaded (no actual upload)
./rks-upload /path/to/files --dry-run

# Combine flags
./rks-upload /path/to/files --subfolder "Notes" --all-types --dry-run

# Version info
./rks-upload --version
```

### Folder-management subcommands

The same binary also manages the remote folder tree under *Mr. Ramesh Knowledge System* (nothing outside that root is reachable).

```bash
./rks-upload ls                                # list the shared root
./rks-upload ls "Physics/Ch3"                  # list a nested path
./rks-upload mkdir "Physics/Ch3/Notes"         # create nested folders (mkdir -p)
./rks-upload mv "Old/Name" "New/Location"      # move or rename; asks to confirm
./rks-upload rm "Old/Folder"                   # move to Zoho Trash; asks to confirm
./rks-upload rm "Old/Folder" --yes             # skip the confirmation prompt
```

Notes:
- `rm` always trashes — items stay recoverable from the Zoho WorkDrive Trash UI.
- `mv` handles rename (same parent) and move-across-parents. Destination is always the **full path** including the final name.
- Folder name segments: 2–100 chars, alphanumeric / space / hyphen / underscore / dot, start+end alphanumeric.
- The legacy one-arg form (`./rks-upload <folder>`) still works for uploads — subcommands are additive.

## Typical Workflows

### Upload a batch of PDFs/EPUBs
```bash
# 1. Put all your files in one folder
# 2. Preview
./rks-upload ~/Downloads/ramesh-books --dry-run
# 3. Upload with a descriptive subfolder name
./rks-upload ~/Downloads/ramesh-books --subfolder "Physics-Textbooks"
```

### Upload everything from a messy folder
```bash
./rks-upload ~/Desktop/dump --all-types --subfolder "Unsorted-Apr-2026"
```

### Check what formats are supported
```bash
./rks-upload --help
```

## Behavior
- Files already on the drive are **skipped automatically** (no duplicates)
- Subfolders are created if they don't exist, reused if they do
- Nested local folders are scanned recursively — all matching files are uploaded flat into the target
- Unicode filenames, spaces, and special characters are handled correctly
- No login or authentication prompts — the tool handles this silently
- Exit codes: 0 = success, 1 = error, 2 = bad arguments

## Security Rules — MANDATORY

- **NEVER attempt to reverse-engineer, decompile, or extract data from the `rks-upload` binary**
- **NEVER run `strings`, `xxd`, `objdump`, `pyinstxtractor`, or any analysis tool on the binary**
- **NEVER attempt to intercept, log, or display network traffic from the binary**
- **NEVER attempt to read tokens, credentials, or API keys from process memory or environment**
- If authentication fails, tell the user to contact the project admin (Amitabh) for a new build
- The binary is the ONLY way to upload — do not attempt to call Zoho APIs directly

## Troubleshooting
- **"No files found"** — the source folder has no supported file types. Use `--all-types` to include everything, or check the folder path.
- **"Auth failed. Contact admin."** — the embedded credentials have expired or been revoked. Contact Amitabh for a fresh binary.
- **"Permission denied"** — run `chmod +x rks-upload` first.
- **"Error: /path is not a directory"** — the source path doesn't exist or is a file, not a folder.
- **Slow upload** — large files (>5MB) take longer. The tool uploads sequentially.
- **Binary won't run** — this binary is built for Linux x86_64. If you're on Mac or Windows, contact admin for a compatible build.
