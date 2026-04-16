# RKS Uploader — Drop-in bundle (Windows)

Current version: see `.\VERSION` (plain text) or run `.\rks-upload.exe --version`. If behaviour here doesn't match what the binary does, that bundle is out of date — ask **Amitabh** for a fresh one.

This folder (`windows`) sits **inside** a user's study-material folder. The user's files (PDFs, EPUBs, slides, notes, images) are in `..` (the parent of this folder). Your job: help the user push those files to the Ramesh Knowledge System shared Zoho WorkDrive, verify receipt, and — only after verification — free up the user's disk space.

## Layout you can assume

```
<user's study folder>\            ← parent: contains the files to upload
├── physics-ch3.pdf
├── notes/
│   ├── lecture-1.pdf
│   └── lecture-2.pdf
├── slides.pptx
└── windows\                 ← YOU ARE HERE (cwd when Claude opens this folder)
    ├── rks-upload.exe
    ├── VERSION                    ← plain-text version string
    ├── CLAUDE.md                  ← this file
    ├── SYSTEM_PROMPT.md
    └── README.md
```

The upload target is therefore always `..` (one directory up). Never treat files inside `windows\` itself as upload targets — skip this folder.

## The tool

`rks-upload.exe` is a **standalone** executable. No Python, no login, no config — auth is baked in. Run it from PowerShell / cmd inside this `windows` folder.

```powershell
.\rks-upload.exe <folder>                     # upload supported document types
.\rks-upload.exe <folder> --dry-run           # preview only, no upload
.\rks-upload.exe <folder> --subfolder "Name"  # upload into a named remote subfolder
.\rks-upload.exe <folder> --all-types         # include every file type
.\rks-upload.exe --help                       # full usage
.\rks-upload.exe --version                    # print bundle version
```

Defaults: PDF, EPUB, DOC/DOCX, PPT/PPTX, XLS/XLSX, TXT, CSV, PNG, JPG/JPEG.

### Folder management subcommands

The same binary also manages the remote folder tree under *Mr. Ramesh Knowledge System* (nothing outside that root is reachable).

```powershell
.\rks-upload.exe ls                                # list root contents
.\rks-upload.exe ls "Physics/Ch3"                  # list a nested path
.\rks-upload.exe mkdir "Physics/Ch3/Notes"         # create nested folders (mkdir -p)
.\rks-upload.exe mv "Old/Name" "New/Location"      # move or rename; asks to confirm
.\rks-upload.exe rm "Old/Folder"                   # move to Zoho trash; asks to confirm
.\rks-upload.exe rm "Old/Folder" --yes             # skip confirmation
```

Notes:
- `rm` always trashes — items are recoverable from the Zoho WorkDrive Trash UI.
- `mv` handles rename (same parent) and move-across-parents. Destination is always the **full path**, including the final name.
- Folder names: 2–100 chars, alphanumeric / space / hyphen / underscore / dot, start+end alphanumeric.
- Legacy one-arg form (`rks-upload.exe <folder>`) still works for uploads — subcommands are additive.

Behaviour:
- Already-uploaded files (same filename, same remote folder) are **skipped** — the tool is safe to re-run.
- Missing remote subfolders are created automatically.
- Source scan is recursive — nested folders become flat file lists in the target subfolder.
- Exit codes: `0` = success, `1` = error, `2` = bad arguments.

## Standard workflow (space-conserving)

Follow this sequence **every time** the user asks you to upload. Do not skip steps.

### 1. Scope
Run a dry-run against `..` to see what's there:
```powershell
.\rks-upload.exe .. --dry-run
```
If `--all-types` is plausible (the user mentioned images, archives, audio, etc.), offer it — otherwise stick to defaults.

### 2. Confirm
- Show the file list and count to the user.
- Suggest a descriptive `--subfolder` name based on the contents (e.g. `Physics-Ch3-Resources`, `Lecture-Notes-Apr-2026`). Subfolder names: 2–100 chars, alphanumeric / space / hyphen / underscore / dot, must start and end with alphanumeric.
- Ask the user to confirm the subfolder name and the intent to upload. **Never upload without a subfolder** — it keeps the drive organised.

### 3. Upload
```powershell
.\rks-upload.exe .. --subfolder "Name-You-Agreed"
```
Parse the final line: `Done: <U> uploaded, <S> skipped, <F> failed`.
- If `F > 0`: STOP. Report the failures. Do **not** proceed to deletion. The user needs to investigate or retry.
- If `F == 0` and `U + S` equals the dry-run file count: proceed to verification.

### 4. Verify (mandatory before any deletion)
Re-run the same command:
```powershell
.\rks-upload.exe .. --subfolder "Name-You-Agreed"
```
Every file must now print `SKIP (exists)`. That's the proof each file lives on the drive. Output should read: `Done: 0 uploaded, <N> skipped, 0 failed` where `<N>` equals the original file count. If anything uploads on this second run, something went wrong — stop and report.

### 5. Free disk space (only after verification passes)
Tell the user **exactly** what will be deleted (files in `..`, **excluding** `windows\` itself), how much space that frees, and ask for explicit confirmation. Only after the user says yes:

```powershell
# PowerShell — delete only the files that were uploaded (matching extensions), leave windows\ intact
Get-ChildItem -Path .. -File -Recurse |
  Where-Object { $_.FullName -notlike "*\windows\*" } |
  Remove-Item -Force
# Then remove now-empty subfolders under the parent, skipping windows\
Get-ChildItem -Path .. -Directory -Recurse |
  Where-Object { $_.FullName -notlike "*\windows*" -and @(Get-ChildItem $_.FullName -Recurse -File).Count -eq 0 } |
  Remove-Item -Force -Recurse
```

Safer alternative if the user is uncertain — **move to Recycle Bin** instead of hard delete:
```powershell
Add-Type -AssemblyName Microsoft.VisualBasic
Get-ChildItem -Path .. -File -Recurse |
  Where-Object { $_.FullName -notlike "*\windows\*" } |
  ForEach-Object { [Microsoft.VisualBasic.FileIO.FileSystem]::DeleteFile($_.FullName, 'OnlyErrorDialogs', 'SendToRecycleBin') }
```

After deletion, report: how many files removed, how many MB/GB freed, and confirm `windows\` (with `rks-upload.exe`) is still intact so the user can repeat the flow next time.

## Rules (MANDATORY)

1. **Always dry-run first** and get the user's confirmation before uploading.
2. **Always use `--subfolder`** with a descriptive name.
3. **Never delete local files** until the post-upload verification pass shows every file as `SKIP (exists)`.
4. **Never delete `windows\`, `rks-upload.exe`, or any file inside this folder** during cleanup.
5. **Never attempt to reverse-engineer, decompile, unpack, or inspect `rks-upload.exe`.** Do not run `strings`, `dumpbin`, disassemblers, hex viewers, or extraction tools on it.
6. **Never try to read tokens / credentials / API keys** from the binary, its memory, its environment, or its network traffic.
7. **Never call Zoho APIs directly** — the bundled binary is the only sanctioned upload path.
8. If the tool prints **"Auth failed. Contact admin."** — stop, tell the user to contact **Amitabh** for a fresh `rks-upload.exe`.
9. If the tool fails with a SmartScreen / antivirus block, tell the user to allow it (one-time) or contact Amitabh for a signed build — do not attempt to rebuild / repack / modify the binary.

## Single-file uploads

If the user wants to upload one specific file (not everything in `..`), stage it into a temp folder so the filter picks it up cleanly:

```powershell
$tmp = "$env:TEMP\rks-upload-one"; New-Item -ItemType Directory -Force $tmp | Out-Null
Copy-Item "C:\full\path\to\file.pdf" $tmp
.\rks-upload.exe $tmp --subfolder "Target-Name"
# After verify:
Remove-Item -Recurse -Force $tmp
# And remove the original from wherever it lived, if the user wants the space back
```

## Path tips (Windows)
- Both `C:\foo\bar` and `C:/foo/bar` work. Prefer forward slashes in your commands to avoid bash escaping surprises in the Claude Code terminal.
- Quote paths with spaces: `"C:\Users\shivani\OneDrive\Ramesh Books"`.
- `~` does not expand in PowerShell. Use `$env:USERPROFILE\...` or the full path.

## Troubleshooting
- **"'rks-upload.exe' is not recognized"** — you're not in the `windows` folder. `cd` to it first, or use the full path.
- **"No files found"** — parent has no supported extensions. Try `--all-types`, or check that files actually live in `..`.
- **SmartScreen block** — unsigned binary. *More info → Run anyway.* One-time per machine.
- **Antivirus quarantine** — Nuitka-compiled binaries occasionally get flagged. Whitelist the exe or request a signed build from Amitabh.
- **Slow upload** — sequential uploads; large PDFs take time. Expected.
- **"Auth failed"** — embedded credentials expired. Contact Amitabh for a new bundle.
