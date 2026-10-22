# File Comparison & Backup Utility

> Fast, reliable file comparison and backup tool for large folder structures.
>
> Compare files by **name and size**, generate detailed reports, and build backup sets containing only files that are missing from another location.

---

## Overview

This utility helps answer a simple question:

> **"Which files from my source folder do not already exist somewhere in my target folder structure?"**

The tool recursively scans folder trees, compares files using filename and file size, and generates a report showing:

- Files found in the target structure
- Files not found in the target structure
- Analysis statistics
- Full duplicate locations

It can then use the generated report to create a backup package containing only the missing files.

---

## Why Use This Tool?

Typical use cases include:

- 📸 Photo archive consolidation
- 💾 Backup verification
- 📁 File migration projects
- 🎵 Media library deduplication
- 📄 Document repository auditing
- 🗄️ Storage cleanup initiatives
- ☁️ Cloud migration preparation

---

## Key Features

- ✅ Recursive folder scanning
- ✅ Comparison based on filename and file size
- ✅ Fast indexed lookups
- ✅ Progress bars for long-running scans
- ✅ JSON report generation
- ✅ Detailed duplicate reporting
- ✅ Backup creation for missing files
- ✅ Preservation of original folder hierarchy
- ✅ Windows-friendly paths
- ✅ Low memory footprint
- ✅ Python 3.15+ compatible

---

# How It Works

The comparison process uses two attributes:

- File name
- File size

A file is considered a match when both values are identical.

### Example

Source:

```text
T:\Pictures\Vacation\IMG_001.jpg
```

Target:

```text
D:\Archive\Photos\IMG_001.jpg
```

If both files have the same size, they will be considered a match regardless of:

- Creation date
- Modified date
- Folder location

---

# Installation

## Requirements

- Python 3.15 or newer
- tqdm

Verify Python installation:

```bash
python --version
```

---

## Install Dependencies

```bash
pip install tqdm
```

or

```bash
python -m pip install tqdm
```

---

# Operating Modes

The utility supports two modes.

| Mode | Description |
|--------|--------|
| **Read** (`r`) | Analyze files and generate a report |
| **Apply** (`a`) | Copy files marked as not found |

---

# Mode 1: Read (Analysis)

Read mode scans the source folder and checks whether each source file exists somewhere inside the target folder structure.

---

## Basic Example

```bash
python run.py -s "T:\Pictures" -t "D:\Archive"
```

---

## Analyze Only Specific File Types

### Images

```bash
python run.py -s "T:\Pictures" -t "D:\Archive" -ft "jpg,jpeg,png"
```

### Documents

```bash
python run.py -s "T:\Documents" -t "D:\Archive" -ft "pdf,docx,xlsx"
```

### Multiple Types

```bash
python run.py -s "T:\Data" -t "D:\Backup" -ft "zip,7z,rar,iso"
```

---

# What Happens During Analysis?

## Step 1 - Count Target Files

The tool first determines the number of files that need to be indexed.

Example:

```text
Counting target files...

Target files to index: 52,341
```

---

## Step 2 - Build Target Index

An optimized in-memory index is created to speed up lookups.

Example:

```text
Target indexing: 100%|████████████████████| 52341/52341
```

---

## Step 3 - Count Source Files

The number of files to analyze is displayed.

Example:

```text
Counting source files...

Source files to analyse: 12,458
```

---

## Step 4 - Analyze Source Files

A live progress bar is displayed.

Example:

```text
Analysing: 100%|████████████████████████| 12458/12458
```

---

## Step 5 - Display Results

Two result sections are displayed.

### Duplicates

Files already found somewhere in the target structure.

Example:

```text
============================================================
DUPLICATES
============================================================

SRC: T:\Pictures\1234567.jpg
   -> D:\Archive\1234567.jpg
   -> D:\Archive\Vacation\1234567.jpg
```

---

### Not Found

Files that could not be located in the target structure.

Example:

```text
============================================================
NOT FOUND
============================================================

T:\Pictures\Family\IMG0001.jpg
T:\Pictures\Family\IMG0002.jpg
```

---

## Step 6 - Generate Report

A JSON report is created automatically.

Example:

```text
analyse_20261021_221530.json
```

---

## Step 7 - Final Summary

A summary is displayed as the last output of the execution.

Example:

```text
============================================================
ANALYSIS COMPLETED
============================================================
Total analysed : 12,458
Found          : 11,620
Not found      : 838
============================================================
```

---

# Mode 2: Apply (Backup Missing Files)

Apply mode uses a previously generated report and copies only the files listed in the **notfound** section.

This is useful for creating a backup package containing only files that do not already exist elsewhere.

---

## Basic Example

```bash
python run.py \
-s "T:\Pictures" \
-op a \
-r "analyse_20261021_221530.json" \
-d "D:\Backup"
```

---

# Folder Structure Preservation

The original folder structure is maintained.

## Example

Source root:

```text
T:\Pictures
```

File reported as missing:

```text
T:\Pictures\Animals\Cats\cat001.jpg
```

Destination root:

```text
D:\Backup
```

Result:

```text
D:\Backup\Animals\Cats\cat001.jpg
```

---

# Generated Report

Reports are saved in JSON format.

Example:

```json
{
    "run": {
        "source": "T:\\Pictures",
        "target": "D:\\Archive",
        "operation": "r"
    },
    "statistics": {
        "total_analysed": 12458,
        "found": 11620,
        "notfound": 838
    },
    "duplicates": [
        {
            "src": "T:\\Pictures\\1234567.jpg",
            "targets": [
                "D:\\Archive\\1234567.jpg",
                "D:\\Archive\\Vacation\\1234567.jpg"
            ]
        }
    ],
    "notfound": [
        "T:\\Pictures\\Animals\\Cats\\cat001.jpg"
    ]
}
```

---

# Command Reference

## Parameters

| Parameter | Description |
|------------|------------|
| `-s`, `--source` | Source folder |
| `-t`, `--target` | Target folder |
| `-ft` | Comma-separated file extensions |
| `-op`, `--operation` | `r` (read) or `a` (apply) |
| `-r`, `--report` | JSON report file |
| `-d`, `--destination` | Destination folder |

---

## Display Help

```bash
python run.py --help
```

---

# Examples

## Analyze an Entire Photo Archive

```bash
python run.py -s "T:\Pictures" -t "D:\Archive"
```

---

## Analyze Only Images

```bash
python run.py -s "T:\Pictures" -t "D:\Archive" -ft "jpg,jpeg,png"
```

---

## Analyze Only Documents

```bash
python run.py -s "T:\Documents" -t "D:\Archive" -ft "pdf,docx,xlsx"
```

---

## Analyze Compressed Files

```bash
python run.py -s "T:\Backups" -t "D:\Repositories" -ft "zip,7z,rar"
```

---

## Build Backup of Missing Files

```bash
python run.py \
-s "T:\Pictures" \
-op a \
-r "analyse_20261021_221530.json" \
-d "D:\Backup"
```

---

# Safety Checks

The utility performs several validations before processing.

## Source Folder Must Exist

Example:

```text
ERROR: Source folder not found: T:\Pictures
```

---

## Target Folder Must Exist

Example:

```text
ERROR: Target folder not found: D:\Archive
```

---

## Source and Target Cannot Be the Same

Example:

```text
ERROR: Source and target folders cannot be the same.
```

---

## Target Cannot Be Inside Source

Example:

```text
Source:
T:\Pictures

Target:
T:\Pictures\Archive
```

Result:

```text
ERROR: Target folder cannot be a subfolder of source folder.
```

---

## Invalid Report File

Example:

```text
ERROR: Invalid JSON report file.
```

---

## Destination Folder Must Be Empty

Example:

```text
ERROR: Destination folder must be empty.
```

This prevents accidental overwrites.

---

# Performance

The tool is optimized for large datasets.

Instead of searching the target folder for every source file, an index is created using:

```python
(filename.lower(), file_size)
```

This allows near constant-time lookups and makes the utility suitable for processing:

- Tens of thousands of files
- Hundreds of thousands of files
- Deep folder hierarchies

---

# Best Practices

For the best performance:

- Store reports after each analysis
- Use file extension filters whenever possible
- Run analysis before copying
- Verify report statistics before applying
- Keep destination folders empty when using Apply mode

---

# License

See the project's `LICENSE` file for licensing information.

---

# Contributing

Contributions, issues, and feature requests are welcome.

If you encounter a bug, please include:

- Python version
- Operating system
- Command executed
- Complete error message
- Sample folder structure if applicable

---

## Quick Start

```bash
pip install tqdm

python run.py -s "T:\Pictures" -t "D:\Archive"

python run.py \
-s "T:\Pictures" \
-op a \
-r "analyse_YYYYMMDD_HHMMSS.json" \
-d "D:\Backup"
```

Happy file hunting! 🚀