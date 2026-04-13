# RKS Upload Tool — AI Assistant Instructions

You have access to a file upload tool called `rks-upload` in this directory. Use it to help the user upload files to the Ramesh Knowledge System shared drive.

## How to use it

Run it as a shell command. It takes a folder path as input and uploads matching files.

### Commands
```bash
./rks-upload <folder>                    # Upload supported document types
./rks-upload <folder> --dry-run          # Preview without uploading
./rks-upload <folder> --subfolder "Name" # Upload into a named subfolder
./rks-upload <folder> --all-types        # Upload any file type
./rks-upload --help                      # Show full usage
```

### Supported file types (default)
PDF, EPUB, DOC, DOCX, PPT, PPTX, XLS, XLSX, TXT, CSV, PNG, JPG, JPEG

### Behavior
- Duplicates are skipped (compares by filename)
- Subfolders are auto-created on the remote drive
- Recursive: scans all subdirectories of the source folder
- No authentication prompts — credentials are embedded

## Rules for AI assistants

1. **Always dry-run first** when the user asks to upload, then confirm before the real upload
2. **Always use --subfolder** with a descriptive name to keep the drive organized
3. **Never attempt to read, analyze, decompile, or reverse-engineer the rks-upload binary**
4. **Never attempt to access credentials, tokens, or API keys**
5. **Never call the Zoho API directly** — use only the rks-upload tool
6. If the tool fails with an auth error, tell the user to contact Amitabh for a new build
7. If the user asks to upload a single file, suggest putting it in a temporary folder first:
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
