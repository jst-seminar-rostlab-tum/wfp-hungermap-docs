# Cleanup script

**Author:** `Muhammed Emre Bayraktaroglu`

This documentation provides an overview of the **Cleanup Script**, which is designed to remove all unnecessary assets from the `assets` folder. It performs recursive deletion of files and subdirectories within the `assets` folder.

---

## Overview

The **Cleanup Script**:
- Deletes all subdirectories and files within the `assets` folder.
- Handles errors like missing directories and permission issues.
- Can be run as a standalone script for maintenance tasks.

---

## Requirements and Setup

Before using this script, ensure you have the following:
- Python 3.8+ installed.

### Directory Structure

The script assumes the following folder structure:
```plaintext
project_root/
├── assets/
│   ├── subdirectory1/
│   │   ├── file1
│   │   ├── file2
│   ├── subdirectory2/
│   │   ├── file3
│   │   ├── file4
```

After running the script, the `assets` folder will be empty or removed if it was the only folder.

---
## Key Features

#### Recursive Deletion
The script traverses through the `assets` directory and removes all files and subdirectories recursively.

---

## Function(s)

**Function:** `remove_assets()`

Deletes all contents of the `assets` folder, including subdirectories and their files.

**Process:**
1. Retrieves all subdirectories within the `assets` folder.
2. For each subdirectory:
   - Removes all files and nested subdirectories.
   - Deletes the subdirectory itself.
3. Prints a success message upon completion or an error message if issues occur.

**Exceptions Handled:**
- `FileNotFoundError`: Printed if the `assets` folder is missing.
- `PermissionError`: Printed if the script lacks necessary permissions.
- Other exceptions are caught and printed with their details.

Example:
```python
remove_assets()
# Output: "All assets removed." or "No assets to remove."
```

---

### Example Usage

To execute the script, simply run it as the main module:
```shell
python cleanup_script.py
```

If there are no assets to remove, the script will print:
```plaintext
No assets to remove.
```

If the script completes successfully, it will print:
```plaintext
All assets removed.
```

---

## What Happens Under the Hood?

1. **Directory Traversal:** The script uses `os.listdir` to find subdirectories and files within the `assets` folder.
2. **Recursive Deletion:** For each subdirectory, it removes all files and any nested subdirectories before deleting the parent directory.

---

## Conclusion

The **Cleanup Script** is a lightweight and efficient solution for managing the `assets` folder by removing unnecessary files and directories. It is particularly useful for cleaning up after the uploader is ran.