# RKS Upload Tool

Upload files to the Ramesh Knowledge System shared drive.

## Setup
```bash
chmod +x ./rks-upload
```

## Usage
```bash
./rks-upload <folder>                              # Upload documents
./rks-upload <folder> --dry-run                    # Preview only
./rks-upload <folder> --subfolder "Name"           # Upload to subfolder
./rks-upload <folder> --all-types                  # All file types
./rks-upload <folder> --subfolder "Name" --dry-run # Combine flags
./rks-upload --help                                # Full help
```

## Supported formats
PDF, EPUB, DOC, DOCX, PPT, PPTX, XLS, XLSX, TXT, CSV, PNG, JPG, JPEG

Use `--all-types` to bypass the filter.

## Notes
- Duplicates are skipped automatically
- Subfolders are created on the drive if they don't exist
- No login needed — authentication is built in
- For issues, contact project admin (Amitabh)
