# Data Quality & Translation Tool

## Overview

This Python project automates the data cleaning and quality assurance process for the **BRAC RMG Workers Impact Evaluation**. It ingests raw survey data (SPSS format), applies complex logic checks to identify inconsistencies, and generates formatted Excel error logs for the field team to review.

Additionally, it includes a module to extract and prepare open-ended text responses for translation.

## Features

### 1. Automated Error Logging

The script scans the dataset for various logic errors and aggregates them into a **Master Error Log**:

* **Logic Checks:**
* **Age vs. Experience:** Flags if (Age - Experience) < 14 years.
* **Household Composition:** Flags high earner ratios (>=70%) or single-person households.
* **Interview Duration:** Flags interviews shorter than 25 mins or longer than 45 mins.
* **Consistency:** Verifies app usage frequency against specific action frequencies (e.g., checking transactions).
* **Missing Data:** Flags cases where follow-up questions (G2/G2a) are missing despite valid app usage.



### 2. Intelligent Formatting

The output Excel files are automatically styled for ease of use by the field team:

* **🔴 Burgundy:** Columns containing the issue/error data.
* **🟢 Green:** "Correction" columns (visual cue for data entry).
* **🟠 Orange:** "Comment" columns (visual cue for notes).
* **Dynamic Widths:** Column widths adjust automatically based on content type.

### 3. Translation Kit Generator

* Extracts specified string variables (e.g., "Other" specify text).
* Filters out blank responses.
* Sorts text in descending order to group identical responses.
* Generates a formatted workbook with dedicated columns for translators.

---

## Prerequisites

Ensure you have **Python 3.x** installed along with the following libraries:

```bash
pip install pandas xlsxwriter pyreadstat

```

* `pandas`: For data manipulation.
* `xlsxwriter`: For advanced Excel formatting (colors, borders).
* `pyreadstat`: For reading SPSS (`.sav`) files and metadata.

---

## Directory Structure

The script expects (and creates) the following folder structure:

```text
D:/OneDrive/Dalberg Research Projects/BRAC/
├── Error Logs/
│   └── [YYYY-MM-DD]/            # Auto-generated daily folder
│       └── BRAC RMG Workers Impact Evaluation issues [Date].xlsx
└── 4. Data/
    └── Translations/            # Auto-generated folder
        └── BRAC_Translation_Request_[Date].xlsx

```

---

## Usage

### 1. Setting Up Paths

At the top of the script, ensure your base paths are correct:

```python
output_path = "D:/OneDrive/Dalberg Research Projects/BRAC/Error Logs"
Working_path = "D:/OneDrive/Dalberg Research Projects/BRAC"

```

### 2. Running Logic Checks

The script initializes a `master_error_log` dictionary. Each logic block adds a new dataframe to this bucket:

```python
# Example Logic
issue_mask = Age_exp < 14
# ... processing ...
master_error_log['Age_Check'] = final_issue_data

```

### 3. Generating Outputs

* **Error Log:** Calls `save_master_workbook()` to merge all checks into one Excel file with color coding.
* **Translations:** Calls `save_translation_workbook()` to create the translation request file.

---

## Key Functions

### `save_master_workbook(...)`

Merges all error dataframes into a single workbook.

* **Inputs:** `error_collection`, `label_dict`, `folder_path`, `keep_vars`.
* **Output:** A multi-tab Excel file where each tab represents a specific logic check.
* **Styling:** Applies specific headers and passes data to `apply_worksheet_styling` for row-level formatting.

### `apply_worksheet_styling(...)`

Handles the visual presentation of the data rows.

* **Logic:**
* **Corrections:** Turns **Green** only when data is entered.
* **Comments:** Turns **Orange** only when text is present.
* **Issues:** Highlights problem columns in **Burgundy**.



### `save_translation_workbook(...)`

Creates the translation request file.

* **Logic:**
* Filters out empty rows.
* Sorts text descending.
* Creates a "Translation" column with a **Green** header.
* Includes original question labels for context.

## Author

* **Veronica** - *Development & Logic Implementation*
