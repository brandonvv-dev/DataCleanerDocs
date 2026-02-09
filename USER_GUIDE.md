# DataCleaner User Guide

**Version 1.0.0**

Complete guide to using DataCleaner for cleaning your CSV and Excel files.

---

## Table of Contents

1. [Getting Started](#1-getting-started)
2. [The Main Interface](#2-the-main-interface)
3. [Importing Data](#3-importing-data)
4. [Cleaning Data](#4-cleaning-data)
5. [Removing Duplicates](#5-removing-duplicates)
6. [Validating Data](#6-validating-data)
7. [Exporting Data](#7-exporting-data)
8. [SQL Export](#8-sql-export)
9. [Working with Profiles](#9-working-with-profiles)
10. [Command-Line Interface](#10-command-line-interface)
11. [Tips and Best Practices](#11-tips-and-best-practices)
12. [Troubleshooting](#12-troubleshooting)

---

## 1. Getting Started

### First Launch

When you first open DataCleaner, you'll see a spotlight onboarding tour that walks you through the main features. Follow the prompts to learn the basics:

1. **Welcome** - Introduction to DataCleaner
2. **Import** - How to load your data files
3. **Preview** - Understanding the data table
4. **Clean** - Available cleaning options
5. **Export** - Saving your cleaned data
6. **Ready** - You're set to start!

You can skip this tour at any time. To see it again, delete the file `%LOCALAPPDATA%\DataCleaner\onboarding.json`.

### System Requirements

- Windows 10 or later (64-bit)
- 4 GB RAM minimum (8 GB recommended for large files)
- 100 MB free disk space

### File Size Limits

DataCleaner can handle files up to **500 MB**. For very large files:

| File Size | Warning Level | Notes |
|-----------|---------------|-------|
| < 50 MB | None | Full performance |
| 50-100 MB | Info | Slightly slower |
| 100-250 MB | Caution | Limited undo history |
| 250-500 MB | Warning | Minimal undo history |
| > 500 MB | Blocked | File too large |

---

## 2. The Main Interface

### Layout Overview

```
+------------------+--------------------------------+----------------+
|                  |                                |                |
|   Workflow Tabs  |         Data Preview           |    Column      |
|                  |                                |    Statistics  |
|   1. Import      |   (Your data appears here)     |                |
|   2. Clean       |                                |    - Type      |
|   3. Dedupe      +--------------------------------+    - Nulls     |
|   4. Validate    |  Output | Warnings | Changes   |    - Unique    |
|   5. Export      |                                |    - Top Values|
|   6. SQL Export  |  (Collapsible bottom panel)    |                |
|   Profiles       |                                |                |
|                  |                                |                |
+------------------+--------------------------------+----------------+
|                        Status Bar                                  |
+--------------------------------------------------------------------+
```

### Workflow Tabs (Left Side)

Work through tabs from top to bottom:

1. **Import** - Load your CSV or Excel file
2. **Clean** - Apply data transformations
3. **Dedupe** - Remove duplicate rows
4. **Validate** - Check data quality
5. **Export** - Save cleaned data
6. **SQL Export** - Generate SQL INSERT statements
7. **Profiles** - Manage cleaning profiles

### Data Preview (Center)

Shows your data in a scrollable table:
- Click column headers to see statistics
- Hover over cells to see full values
- Table updates after each operation

### Column Statistics (Right Side)

When you click a column, see:
- **Data Type** - String, Integer, Float, Date, etc.
- **Null Count** - How many empty/null values
- **Unique Count** - Number of distinct values
- **Min/Max** - Range for numeric/date columns
- **Top Values** - Most frequent values

### Bottom Panel

Collapsible panel with three tabs:
- **Output** - Current state of your data
- **Warnings** - Import and processing warnings
- **Changes** - Summary of modifications made

Click the collapse button to hide/show this panel.

---

## 3. Importing Data

### Supported File Types

| Extension | Description |
|-----------|-------------|
| .csv | Comma-separated values |
| .tsv | Tab-separated values |
| .txt | Plain text (auto-detect delimiter) |
| .xlsx | Excel 2007+ workbook |

### How to Import

**Method 1: Drag and Drop**
Simply drag a file from Windows Explorer onto the DataCleaner window.

**Method 2: Browse Button**
1. Go to the **Import** tab
2. Click **Browse...**
3. Select your file
4. Click **Open**

**Method 3: Keyboard Shortcut**
Press `Ctrl+O` to open the file dialog.

### Import Options

After selecting a file, you can adjust these settings:

**For CSV/TSV/TXT files:**
- **Encoding** - Usually auto-detected (UTF-8, Windows-1252, etc.)
- **Delimiter** - Usually auto-detected (comma, semicolon, tab, pipe)
- **Has Header** - Whether first row contains column names

**For Excel files:**
- **Sheet** - Select which worksheet to import

### Auto-Detection

DataCleaner automatically detects:
- File encoding (including BOM markers)
- CSV delimiter character
- Quote character style
- Header row location
- Column data types

If detection fails, you can manually override the settings.

### After Import

Once imported, you'll see:
- Your data in the preview table
- File information in the status bar
- Any warnings in the bottom panel

The application automatically:
- Creates a profile of your data
- Counts duplicates
- Identifies potential issues

---

## 4. Cleaning Data

### Available Cleaning Options

Go to the **Clean** tab to see all available transformations:

#### Whitespace Trimming
**What it does:** Removes leading and trailing spaces from all text values.

**Example:**
```
Before: "  John Smith  "
After:  "John Smith"
```

**When to use:** Almost always - extra spaces cause matching and sorting issues.

#### Empty Row Removal
**What it does:** Removes rows where ALL columns are empty or null.

**When to use:** When your data has blank rows between records.

#### Null Token Normalization
**What it does:** Converts various representations of "empty" to actual null values.

**Recognized null tokens include:**
- Empty string `""`
- `NULL`, `null`, `Null`
- `N/A`, `NA`, `n/a`, `na`
- `None`, `NONE`, `none`
- `-`, `--`, `---`
- `.`, `..`
- `#N/A`, `#VALUE!`
- `undefined`
- And 40+ more variations

**Example:**
```
Before: "N/A"
After:  (null)
```

#### Header Normalization
**What it does:** Cleans up column names.

**Options:**
- **Trim** - Remove spaces from column names
- **Lowercase** - Convert to lowercase (optional)
- **Replace Spaces** - Convert spaces to underscores
- **Remove Special Characters** - Remove characters like `/`, `\`, `*`
- **Deduplicate Names** - Add numbers to duplicate column names

**Example:**
```
Before: "First Name ", "First Name", "order/date"
After:  "first_name", "first_name_2", "order_date"
```

#### Numeric Normalization
**What it does:** Standardizes number formats.

Handles:
- European decimals: `1.234,56` -> `1234.56`
- Thousand separators: `1,000,000` -> `1000000`

**Caution:** Ambiguous values like `1,234` (is it 1234 or 1.234?) are preserved as text unless confidence is high.

#### Date Normalization
**What it does:** Converts dates to ISO format (YYYY-MM-DD).

**Supported input formats:**
- `01/02/2024` (respects day-first setting)
- `2024-01-02`
- `Jan 2, 2024`
- `02-Jan-2024`
- And many more

**Output format:** `2024-01-02`

### Running the Clean Operation

1. Check the boxes for transformations you want
2. Click **Clean Data**
3. Review changes in the bottom panel
4. Check the data preview

### Understanding the Results

After cleaning, the Changes panel shows:
- **Rows before/after** - How many rows were affected
- **Columns renamed** - Header changes
- **Null tokens replaced** - Count of null normalizations
- **Warnings** - Any issues encountered

---

## 5. Removing Duplicates

### What is a Duplicate?

A duplicate is a row that matches another row. You can define what "matches" means:

- **Exact duplicates** - All columns must match
- **Key-based duplicates** - Only specified columns must match

### Deduplication Options

Go to the **Dedupe** tab:

#### Key Columns
Select which columns determine uniqueness:

- **All columns** (default) - Rows must match exactly
- **Specific columns** - e.g., just `email` or `id, name`

#### Keep Strategy
When duplicates are found, which one to keep?

| Strategy | Keeps | Use When |
|----------|-------|----------|
| **First** | First occurrence | Original order matters |
| **Last** | Last occurrence | Latest is most current |
| **Most Complete** | Row with fewest nulls | Data quality varies |

### Running Deduplication

1. Select key columns (or leave empty for exact duplicates)
2. Choose a keep strategy
3. Click **Remove Duplicates**
4. Review the report

### Deduplication Report

After running, you'll see:
- **Duplicates Found** - Total duplicate rows detected
- **Duplicates Removed** - How many were deleted
- **Duplicate Groups** - Number of duplicate sets
- **Sample Duplicates** - Examples of what was removed

---

## 6. Validating Data

### Why Validate?

Validation helps you catch data quality issues before using your data. Find problems like:
- Missing required values
- Invalid email addresses
- Wrong date formats
- Unexpected values

### Available Validation Rules

Go to the **Validate** tab:

#### Required Column
Checks that a column exists in the data.

**Use case:** Ensure critical columns weren't lost during import.

#### Non-Null
Ensures a column has no null/empty values.

**Use case:** Required fields like `customer_id` or `email`.

#### Email Format
Validates email address format.

**Pattern:** `user@domain.com`

**Use case:** Clean up customer contact data.

#### Date Valid
Ensures values can be parsed as dates.

**Use case:** Verify date columns before analysis.

#### Numeric Valid
Ensures values are valid numbers.

**Use case:** Verify price, quantity, or ID columns.

#### Regex Match
Apply a custom pattern.

**Examples:**
- Phone number: `^\d{10}$`
- ZIP code: `^\d{5}(-\d{4})?$`
- Product code: `^[A-Z]{3}-\d{4}$`

#### Value in List
Ensures values are from an allowed set.

**Example:** Status must be `active`, `pending`, or `closed`.

#### Unique
Ensures no duplicate values in a column.

**Use case:** Verify `id` or `email` columns have no duplicates.

### Running Validation

1. Add rules by selecting type and column
2. Configure rule parameters
3. Click **Validate**
4. Review results

### Validation Results

Results show for each rule:
- **Pass/Fail** status
- **Violation count** - How many rows failed
- **Sample failures** - Example problematic values

Exit codes for automation:
- **0** - All rules passed
- **2** - Some rules failed

---

## 7. Exporting Data

### Export Formats

Go to the **Export** tab:

| Format | Extension | Notes |
|--------|-----------|-------|
| CSV | .csv | Universal, text-based |
| Excel | .xlsx | Formatted workbook |

### CSV Export Options

- **Delimiter** - Default is comma, can use semicolon or tab
- **CSV Injection Protection** - Prefixes formula-like values with `'`

**CSV Injection Protection:**

Without protection, a value like `=SUM(A1:A10)` could execute as a formula when opened in Excel. Protection adds a single quote prefix: `'=SUM(A1:A10)`.

### Export with Reports

Check **Include Reports** to generate:

- **change_report.json** - Detailed JSON of all changes
- **summary.txt** - Human-readable summary
- **validation.json** - Validation results (if run)

### Running Export

1. Click **Browse** to select output location
2. Choose format (CSV or Excel)
3. Optional: Check "Include Reports"
4. Click **Export**

---

## 8. SQL Export

### What is SQL Export?

Generate SQL INSERT statements to load your cleaned data into a database.

### Setting Up SQL Export

Go to the **SQL Export** tab:

#### 1. Define Target Table

Enter the table name where data will be inserted:
- **Table Name** - e.g., `customers`
- **Schema** - e.g., `dbo` (default)

#### 2. Define Columns

Click **Add Column** to define each target column:
- **Name** - Column name in the database
- **Type** - SQL data type (VARCHAR, INT, DATETIME, etc.)
- **Length** - For VARCHAR columns
- **Nullable** - Whether NULLs are allowed

#### 3. Map Source to Target

For each target column, select which source column maps to it.

### SQL Options

- **Include Transaction** - Wrap in BEGIN/COMMIT
- **Include Identity Insert** - Add SET IDENTITY_INSERT statements
- **Batch Size** - Rows per GO statement (default: 1000)

### Output

The generated SQL file contains:
```sql
BEGIN TRANSACTION;

INSERT INTO dbo.customers (id, name, email)
VALUES (1, 'John', 'john@example.com');

INSERT INTO dbo.customers (id, name, email)
VALUES (2, 'Jane', 'jane@example.com');

-- ... more rows ...

GO

COMMIT;
```

---

## 9. Working with Profiles

### What is a Profile?

A cleaning profile saves all your settings so you can reuse them:
- Which transformations to apply
- Header normalization options
- Null token list
- Validation rules
- Export settings

### Creating a Profile

1. Configure your cleaning settings on the Clean tab
2. Go to **Profiles** tab
3. Enter a profile name
4. Click **Save Profile**

### Loading a Profile

1. Go to **Profiles** tab
2. Select a profile from the list
3. Click **Load Profile**
4. All settings are now restored

### Managing Profiles

- **Create** - Save current settings as new profile
- **Delete** - Remove a saved profile
- **Export** - Save profile to a JSON file
- **Import** - Load profile from a JSON file

### Profile Location

Profiles are stored in:
```
%LOCALAPPDATA%\DataCleaner\profiles\
```

---

## 10. Command-Line Interface

DataCleaner includes a powerful CLI for automation and power users.

### Basic Usage

```bash
# Show version
datacleaner --version

# Show help
datacleaner --help

# Launch GUI explicitly
datacleaner --gui
```

### Commands Reference

#### datacleaner info

Show file statistics:
```bash
datacleaner info data.csv
datacleaner info data.xlsx --json
datacleaner info data.csv --profile  # Include column statistics
```

#### datacleaner clean

Apply cleaning transformations:
```bash
# Using a profile
datacleaner clean data.csv --profile monthly --output cleaned.csv

# Using individual options
datacleaner clean data.csv --trim --normalize-nulls -o cleaned.csv

# Preview changes without writing
datacleaner clean data.csv --dry-run

# Output as JSON for scripting
datacleaner clean data.csv --json
```

**Options:**
- `--profile, -p` - Cleaning profile name
- `--output, -o` - Output file path
- `--format` - Output format (csv, xlsx)
- `--trim/--no-trim` - Whitespace trimming
- `--normalize-nulls/--no-normalize-nulls` - Null normalization
- `--remove-empty/--no-remove-empty` - Empty row removal
- `--dry-run` - Preview without writing
- `--json` - JSON output
- `--quiet, -q` - Minimal output
- `--stdin` - Read from stdin
- `--stdout` - Write to stdout

#### datacleaner dedupe

Remove duplicates:
```bash
# By specific columns
datacleaner dedupe data.csv --columns email --keep first -o deduped.csv

# By all columns (exact duplicates)
datacleaner dedupe data.csv --keep most_complete

# Preview duplicates
datacleaner dedupe data.csv --dry-run
```

**Options:**
- `--columns, -c` - Key columns (comma-separated)
- `--keep` - Strategy: first, last, most_complete
- `--output, -o` - Output file path
- `--dry-run` - Preview without removing
- `--json` - JSON output

#### datacleaner validate

Validate data quality:
```bash
# Email validation
datacleaner validate data.csv --email email_col

# Multiple rules
datacleaner validate data.csv --required name,id --unique id

# With JSON output for CI/CD
datacleaner validate data.csv --email email --json
if [ $? -eq 2 ]; then echo "Validation failed"; fi
```

**Options:**
- `--email` - Email column to validate
- `--date` - Date column to validate
- `--numeric` - Numeric column to validate
- `--required` - Required columns (comma-separated)
- `--unique` - Column that must be unique
- `--non-null` - Column that must not have nulls
- `--regex COLUMN PATTERN` - Custom regex validation
- `--json` - JSON output
- `--quiet, -q` - Exit codes only

**Exit Codes:**
- 0 - All validations passed
- 1 - Error (file not found, etc.)
- 2 - Validation failures

#### datacleaner export

Export to different formats:
```bash
# To Excel
datacleaner export data.csv --format xlsx -o result.xlsx

# To SQL
datacleaner export data.csv --format sql --table customers
```

#### datacleaner profile

Manage profiles:
```bash
# List all profiles
datacleaner profile list

# Show profile details
datacleaner profile show monthly

# Create from existing
datacleaner profile create my_profile --from default

# Delete a profile
datacleaner profile delete old_profile

# Export to file
datacleaner profile export monthly -o monthly.json

# Import from file
datacleaner profile import ./my_profile.json
```

### Power-User Features

#### Batch Processing

Process multiple files at once:
```bash
# Clean all CSV files
datacleaner batch clean "data/*.csv" --profile monthly --output cleaned/

# With parallel processing
datacleaner batch clean "data/*.csv" --parallel 4 --output cleaned/

# Continue on errors
datacleaner batch clean "data/*.csv" --continue-on-error --output cleaned/

# Batch validation
datacleaner batch validate "data/*.csv" --required name,id --summary
```

#### Pipe/Stream Mode

Unix-style piping:
```bash
# Pipe through multiple operations
cat data.csv | datacleaner clean --stdin --stdout | datacleaner dedupe --stdin --stdout > final.csv

# Combine with other tools
datacleaner clean data.csv --stdout | csvtool col 1,3,5 - > subset.csv
```

#### Watch Mode

Auto-process new files:
```bash
# Watch a folder
datacleaner watch ./inbox --profile auto_clean --output ./processed

# With archive of originals
datacleaner watch ./inbox --profile monthly --output ./processed --move-originals ./archive

# Custom patterns and interval
datacleaner watch ./inbox --pattern "*.csv" --interval 10 --json
```

Press `Ctrl+C` to stop watching.

#### Dry Run

Preview changes without modifying files:
```bash
datacleaner clean data.csv --profile monthly --dry-run
```

Output shows:
- Rows before/after
- What would change
- Sample of modifications

#### JSON Output

Machine-readable output:
```bash
# Clean with JSON summary
datacleaner clean data.csv --json

# Validate and parse results
datacleaner validate data.csv --email email_col --json | jq '.failed_rules'
```

---

## 11. Tips and Best Practices

### General Workflow

1. **Import** - Load your file, check for warnings
2. **Review** - Look at the data and column statistics
3. **Clean** - Apply transformations step by step
4. **Dedupe** - Remove duplicates if needed
5. **Validate** - Run validation rules
6. **Export** - Save your cleaned data

### Performance Tips

- **Large files**: Close other applications to free memory
- **Very large files**: Use the CLI for better performance
- **Batch processing**: Use `--parallel` for multiple files

### Data Quality Tips

- **Always validate** before using cleaned data
- **Use profiles** to ensure consistent cleaning across files
- **Check warnings** - they often reveal data issues
- **Preview with dry-run** before making changes

### Backup Tips

- DataCleaner never modifies your original files
- Export to a new file, don't overwrite originals
- Use "Include Reports" to document what changed

---

## 12. Troubleshooting

### Import Issues

**"Encoding detection failed"**
- Try manually selecting an encoding (UTF-8, Windows-1252)
- Open in Notepad++, check encoding in bottom-right

**"Delimiter not detected"**
- Manually specify the delimiter
- Check if file has inconsistent formatting

**"File too large"**
- Split the file into smaller parts
- Use the CLI for better memory handling

### Cleaning Issues

**"Values not converting"**
- Check for hidden characters (copy/paste from data)
- Values may be ambiguous (e.g., `1,234` in mixed locales)

**"Dates not recognized"**
- Check the dayfirst setting (01/02/03 = Jan 2 or Feb 1?)
- Try date normalization with different settings

### Export Issues

**"Cannot write file"**
- Close the file in Excel/other applications
- Check write permissions on the folder

**"File not saved"**
- Check available disk space
- Try a different output location

### CLI Issues

**"Command not found"**
- Make sure DataCleaner is in your PATH
- Use `python -m datacleaner` instead

**"Pattern not matching files"**
- Quote glob patterns: `"data/*.csv"` not `data/*.csv`
- Check current directory

### Getting Help

If you encounter issues:
1. Check the warnings panel for details
2. Look in the log file: `%LOCALAPPDATA%\DataCleaner\logs\app.log`
3. Report issues at: https://github.com/BrandonVanVuuren/Excel_Cleaner/issues

---

## Keyboard Shortcuts

| Shortcut | Action |
|----------|--------|
| `Ctrl+O` | Open file |
| `Ctrl+S` | Save/Export |
| `Ctrl+Z` | Undo |
| `Ctrl+Y` | Redo |
| `Alt+F4` | Exit |

---

## Glossary

**CSV** - Comma-Separated Values, a text file format for tabular data

**Delimiter** - Character separating columns (comma, semicolon, tab)

**Encoding** - How text characters are stored (UTF-8, Windows-1252)

**Null** - Empty/missing value

**Profile** - Saved cleaning configuration

**Validation Rule** - A check applied to data to verify quality

---

*DataCleaner - Clean data, clear insights.*

*Copyright 2024 Brandon van Vuuren. All rights reserved.*
