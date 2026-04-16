# RKS Upload Tool — AI Assistant Instructions

You have access to `rks-upload` in this directory — a standalone binary that uploads files to and manages folders on the Ramesh Knowledge System shared Zoho WorkDrive workspace.

Bundle version: check `./VERSION` or `./rks-upload --version`. If behaviour contradicts these instructions, the bundle is outdated — tell the user to request a fresh one from **Amitabh**.

## How to use it

Two modes in the same binary:

**Upload mode** (legacy one-arg form still works):
```bash
./rks-upload <folder>                          # Upload supported document types
./rks-upload <folder> --dry-run                # Preview without uploading
./rks-upload <folder> --subfolder "Path"       # Upload into subfolder (nested OK, e.g. "Physics/Ch3/Notes")
./rks-upload <folder> --all-types              # Upload any file type
```

**Folder-ops subcommands** — manage the remote tree (scoped to the shared root):
```bash
./rks-upload ls [path]                         # List a remote folder (default: root)
./rks-upload mkdir <path>                      # Create nested folders (mkdir -p)
./rks-upload mv <src> <dst>                    # Rename or move; <dst> = full path incl. new name
./rks-upload rm <path>                         # Move to Zoho Trash (recoverable)
./rks-upload rm <path> --yes                   # Skip confirmation prompt
./rks-upload --help                            # Full usage
./rks-upload --version                         # Version info
```

Path rules: relative to the shared root; cannot escape it. Folder name segments: 2–100 chars, alphanumeric / space / hyphen / underscore / dot, start+end alphanumeric.

### Supported upload types (default)
PDF, EPUB, DOC, DOCX, PPT, PPTX, XLS, XLSX, TXT, CSV, PNG, JPG, JPEG

### Behavior
- Duplicates are skipped (compares by filename)
- Subfolders are auto-created on the remote drive (nested paths OK)
- Recursive: scans all subdirectories of the source folder
- No authentication prompts — credentials are embedded
- `rm` trashes (recoverable); `mv` / `rm` prompt before acting unless `--yes`

## Rules for AI assistants

1. **Always dry-run first** when the user asks to upload, then confirm before the real upload
2. **Always use --subfolder** with a descriptive name to keep the drive organized
3. **Prefer `ls` before `rm` / `mv`** — confirm the target exists and is the intended item
4. **For `rm`**, tell the user exactly what will be trashed (and that it's recoverable from Zoho's Trash UI) before running. Use `--yes` only after explicit user confirmation.
5. **For `mv`**, show before/after paths clearly. `<dst>` is a full path including the new name — `mv A/B A/C` renames `B` to `C` inside `A`; `mv A/B X/B` moves `B` under `X`.
6. **Never attempt to read, analyze, decompile, or reverse-engineer the rks-upload binary**
7. **Never attempt to access credentials, tokens, or API keys**
8. **Never call the Zoho API directly** — use only the rks-upload tool
9. If the tool fails with an auth error, tell the user to contact Amitabh for a new build
10. If the user asks to upload a single file, suggest putting it in a temporary folder first:
    ```bash
    mkdir -p /tmp/rks-upload-tmp && cp /path/to/file.pdf /tmp/rks-upload-tmp/
    ./rks-upload /tmp/rks-upload-tmp --subfolder "Target-Folder"
    ```

## Example conversation flow

User: "Upload these PDFs to the drive"
Assistant:
1. Ask which folder contains the PDFs
2. Run `./rks-upload <folder> --dry-run` to preview
3. Show the user what will be uploaded
4. Ask for a subfolder name (suggest one based on content)
5. Run `./rks-upload <folder> --subfolder "Name"`
6. Report results
