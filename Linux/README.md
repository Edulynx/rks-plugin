# RKS Upload Tool

Upload files to (and manage folders on) the Ramesh Knowledge System shared drive.

Version: see `./VERSION` or run `./rks-upload --version`.

## Setup
```bash
chmod +x ./rks-upload
```

## Upload
```bash
./rks-upload <folder>                                    # Upload documents
./rks-upload <folder> --dry-run                          # Preview only
./rks-upload <folder> --subfolder "Name"                 # Upload to subfolder
./rks-upload <folder> --subfolder "Physics/Ch3/Notes"    # Nested subfolder (auto-created)
./rks-upload <folder> --all-types                        # All file types
./rks-upload --help                                      # Full help
./rks-upload --version                                   # Version info
```

## Folder management
```bash
./rks-upload ls                                          # List the shared root
./rks-upload ls "Physics/Ch3"                            # List a nested path
./rks-upload mkdir "Physics/Ch3/Notes"                   # Create nested folders
./rks-upload mv "Old/Name" "New/Location"                # Move or rename (confirms)
./rks-upload rm "Old/Folder"                             # Move to Zoho Trash (confirms)
./rks-upload rm "Old/Folder" --yes                       # Skip confirmation
```

`rm` moves to Zoho Trash — recoverable from the drive's Trash UI. Everything is scoped to the shared root; paths cannot escape it.

## Supported upload formats
PDF, EPUB, DOC, DOCX, PPT, PPTX, XLS, XLSX, TXT, CSV, PNG, JPG, JPEG

Use `--all-types` to bypass the filter.

## Notes
- Duplicates are skipped automatically
- Subfolders are created on the drive if they don't exist (nested paths OK)
- No login needed — authentication is built in
- For issues, contact project admin (Amitabh)
