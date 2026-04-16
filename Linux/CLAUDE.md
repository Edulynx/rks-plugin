# Ramesh Knowledge System — File Uploader

This folder contains `rks-upload`, a standalone tool for uploading files to the shared Zoho WorkDrive folder for the Ramesh Knowledge System project.

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

# Upload into a named subfolder on the drive
./rks-upload /path/to/files --subfolder "Chapter-3-Resources"

# Upload ALL file types, not just documents
./rks-upload /path/to/files --all-types

# Preview what would be uploaded (no actual upload)
./rks-upload /path/to/files --dry-run

# Combine flags
./rks-upload /path/to/files --subfolder "Notes" --all-types --dry-run
```

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
