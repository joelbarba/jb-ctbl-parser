# JB-CTBL-Parser 🌐

A lightweight web-based utility to **convert and normalize financial transaction data** from two sources—**5. BS** (Activo Bank) and **6. N26**—into a unified `.ctbl`-style format.

Created by [Joel Barba](https://github.com/joelbarba), this tool simplifies the process of cleaning, formatting, and combining bank exports, making them ready for downstream parsing or processing.

---

## 🌟 Features

- 📄 Paste raw transaction data from two sources: BS (Activo Bank) and N26
- ⚙️ Automatic:
  - Date parsing and reformatting
  - Sorting by date
  - Formatting into a unified tab-separated `.ctbl`-like format
- 🧼 One-click clearing and clipboard copy
- 💾 LocalStorage persistence for in-progress data

---

## 🚀 Getting Started

Just open the app in a browser! No installation required.

### 📦 Local Deployment

To run it locally:

1. Clone the repo:

   ```bash
   git clone https://github.com/joelbarba/jb-ctbl-parser.git
   cd jb-ctbl-parser/web


---
## 🖥️ How to Use
Paste raw data into either:

1. BS (left side) — data from Activo Bank

2. N26 (right side) — data from N26 export

Click the Parse button for each section or Parse All to combine both.
View the output below or copy to clipboard using Copy to clipboard.

### 🔘 Buttons
Parse — Format and display the section’s data

Parse All — Merge and sort both sources into one list

Clear — Clear the respective input

Copy to clipboard — Copy the formatted result

---
## 🧠 How It Works
Input: Raw tab-separated bank data (copied from spreadsheet exports)

Process:

Splits into lines

Removes blanks

Sorts by transaction date

Formats lines into .ctbl-style tab-separated records

Output: Tabular string displayed below the inputs

### 💡 Format Logic
BS Input:
Expected format:
    ```DD/MM/YYYY<TAB>Description<TAB><TAB>Amount<TAB><TAB>

Transformed into:
    ```Date<TAB>Description<TAB><TAB>Amount<TAB><TAB>5. BS

N26 Input:
Expected TSV export from N26 (with header line)
    ```DD/MM/YYYY<TAB>Description<TAB><TAB>Amount<TAB><TAB>

Transformed into:
    ```Date<TAB>Description<TAB><TAB>Amount<TAB><TAB>6. N26
