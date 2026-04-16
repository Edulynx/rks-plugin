# RKS Uploader — Windows drop-in bundle

Drop this **entire `windows` folder** inside any folder that holds PDFs / EPUBs / slides / notes you want to push to the Ramesh Knowledge System shared drive, then open Claude Code (Windows app) on the `windows` folder. Ask Claude to upload — it will read `CLAUDE.md` and handle the rest, including freeing up disk space once the drive confirms receipt.

## What's in here
- `rks-upload.exe` — standalone uploader (no Python / login / config needed)
- `CLAUDE.md` — instructions Claude reads when you open this folder
- `SYSTEM_PROMPT.md` — stricter rules for the AI assistant
- `README.md` — this file

## Setup (one time, per PC)
1. Copy `windows/` into any study-material folder, e.g. `C:\Users\shivani\Downloads\physics-books\windows\`.
2. Open **Claude Code** (Windows app) → *Open folder* → pick the `windows` subfolder (not the parent). Claude auto-loads `CLAUDE.md`.
3. First time you run `rks-upload.exe`, Windows may show a SmartScreen warning → *More info → Run anyway*. One time only.

## How to use it
Just tell Claude what you want in plain English. Examples:
- "Upload everything in the parent folder to a subfolder called *Physics-Textbooks* on the drive. Free up my disk after it's confirmed."
- "Preview what would be uploaded from the parent folder."
- "Upload these PDFs and delete them locally once they're safely on the drive."

Claude will always dry-run first, confirm with you, upload, re-check that every file is on the drive, then — only after verification — delete them locally.

## Manual use (without Claude)
From a PowerShell prompt in the `windows` folder:

```powershell
# Preview
.\rks-upload.exe .. --dry-run

# Upload parent folder's files to a named subfolder
.\rks-upload.exe .. --subfolder "Physics-Textbooks"

# Upload ALL file types (not just documents)
.\rks-upload.exe .. --all-types --subfolder "Unsorted-Apr-2026"

# Help
.\rks-upload.exe --help
```

Supported defaults: PDF, EPUB, DOC/DOCX, PPT/PPTX, XLS/XLSX, TXT, CSV, PNG, JPG/JPEG. Use `--all-types` for anything else.

## Notes
- Duplicates on the drive are **skipped automatically** (by filename). Safe to re-run.
- Subfolders are created on the drive if missing, reused if present.
- Recursive: scans all subdirectories of the parent folder.
- The binary is self-contained — **do not delete it** unless you're done uploading from this folder.
- For a fresh build (credentials rotated), contact the project admin **Amitabh**.
