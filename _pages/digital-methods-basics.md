---
title: "Digital Methods Basics: Files, Data, and Tools"
permalink: /digital-methods-basics/
author_profile: true
toc: true
toc_sticky: true
toc_label: "On This Page"
---

Digital history tutorials often use terms such as CSV, JSON, API, notebook, repository, and package. You do not need a computer science background to work with these tools, but understanding a few basic concepts makes digital research workflows much easier to follow and troubleshoot.

This page is a reference guide to concepts that appear repeatedly throughout the Digital History Pedagogy Toolkit. You can read it from beginning to end or return to individual sections when you encounter an unfamiliar term in another tutorial.

> **You do not need to memorize these terms.** The goal is to understand what role each one plays in a digital research workflow.

## Quick Reference

| Term | Think of it as | Example in the Toolkit |
| --- | --- | --- |
| CSV | A simple table saved as a text file | Gazetteer or transcription dataset |
| JSON | Structured data organized using named fields and lists | Structured output returned by Gemini |

---

## 1. What Is a File Format?

A **file format** determines how information is organized inside a digital file and how software should interpret it.

You can usually identify a file format by the extension at the end of the filename:

```text
transcription.txt
places.csv
results.json
analysis.ipynb
article.pdf
```

The extension does more than indicate which program might open the file. It also tells us something about how the information inside the file is structured.

For example:

- `.txt` usually contains plain text;
- `.csv` represents tabular data;
- `.json` represents structured data using keys, values, and lists;
- `.ipynb` is a Jupyter Notebook containing code, text, and output;
- `.pdf` preserves the visual layout of a document.

Different formats are useful for different research tasks.

A transcription stored in a `.txt` file may be useful for reading or text analysis. A list of historical places may be easier to work with as a `.csv` file. Data returned by an API may arrive as JSON.

For digital historians, choosing a file format is therefore partly a methodological decision: the format affects what can easily be searched, sorted, compared, processed, or reused.

---

## 2. CSV: Data in Rows and Columns

**CSV** stands for **Comma-Separated Values**.

A CSV file stores tabular data in plain text. It is similar to a spreadsheet: each **row** represents a record, and each **column** represents a field or attribute.

For example:

```csv
record_id,place_name,country
001,Basra,Iraq
002,Kuwait City,Kuwait
003,Bushire,Iran
```

Here:

- `record_id`, `place_name`, and `country` are fields;
- each following line is one record.

This structure is useful for historical datasets because it allows us to organize repeated information consistently.

### CSV in the Toolkit

Several Toolkit workflows use CSV files.

For example, a historical gazetteer might contain:

```text
place name
country
latitude
longitude
source
```

A transcription dataset might contain:

```text
record ID
source file
page number
transcription
```

Python libraries such as `pandas` can load these files and treat them as tables.

For example:

```python
import pandas as pd

df = pd.read_csv("sample_transcriptions.csv")
```

The variable `df` now contains the CSV as a **DataFrame**, which is pandas's table-like data structure.

### One Record per Row

A useful principle when designing a CSV dataset is:

> **Decide what one row represents.**

In the structured historical data tutorial, for example, one row represents **one transcribed archival page**.

In another project, one row might represent:

- one person;
- one place;
- one letter;
- one newspaper article;
- one archival file.

Making this decision clearly helps keep the dataset consistent.

### What Happens When Text Contains Commas?

Historical text often contains commas. CSV files handle this by placing the entire value inside quotation marks.

For example:

```csv
record_id,transcription
001,"The agent travelled to Basra, Kuwait, and Bushire."
```

The commas inside the quotation marks belong to the transcription rather than separating new columns.

Most spreadsheet programs and Python libraries handle this automatically.

### Limitations of CSV

CSV works particularly well for simple tables, but it becomes awkward when one record contains several values of the same type.

Suppose one document mentions three places:

```text
Kuwait
Basra
Bushire
```

A CSV might store them in one cell:

```text
Kuwait; Basra; Bushire
```

This is readable, but the computer now sees that cell as one text string rather than three separate place values.

For more complex structures, another format such as JSON can be more appropriate.

---

## 3. JSON: Structured Data with Keys and Values

**JSON** stands for **JavaScript Object Notation**.

Despite its name, JSON is widely used outside JavaScript. APIs, web applications, and AI systems frequently use JSON because it can represent structured information more flexibly than a simple table.

Consider this historical record:

```json
{
  "record_id": "001",
  "people": [
    "Sheikh Mubarak"
  ],
  "places": [
    "Kuwait",
    "Basra"
  ]
}
```

The structure contains **keys** and **values**.

For example:

```json
"record_id": "001"
```

Here, `record_id` is the key and `001` is its value.

JSON can also contain **lists**, shown inside square brackets:

```json
"places": [
  "Kuwait",
  "Basra"
]
```

This tells the computer that `Kuwait` and `Basra` are two separate values belonging to the same field.

### Why Is JSON Useful?

Compare the same information in CSV:

```csv
record_id,people,places
001,"Sheikh Mubarak","Kuwait; Basra"
```

and JSON:

```json
{
  "record_id": "001",
  "people": [
    "Sheikh Mubarak"
  ],
  "places": [
    "Kuwait",
    "Basra"
  ]
}
```

The CSV is easy to view as a table.

The JSON preserves more of the structure: the computer can tell that the record contains **two separate places**, rather than one text value containing a semicolon.

This becomes especially useful when data contains:

- several people;
- several places;
- lists of events;
- nested information;
- output returned by an API.

### JSON in the Toolkit

In the structured historical data tutorial, we ask Gemini to return information in JSON format:

```json
{
  "people": [],
  "places": [],
  "dates_times": [],
  "organizations_offices": [],
  "actions_events": []
}
```

Using the same structure for every document makes it easier to process multiple AI-generated results systematically.

### CSV or JSON?

Neither format is inherently better. They are useful for different kinds of data and different stages of a research workflow.

| CSV | JSON |
| --- | --- |
| Best suited to simple tables | Better suited to more complex structures |
| Easy to open in spreadsheet software | Common in APIs and programming workflows |
| One row usually represents one record | One object can represent one record |
| Works well when fields contain simple values | Handles lists and nested information more naturally |

In many digital history workflows, researchers use **both**. JSON may preserve the structure of the original data, while CSV provides a convenient version for viewing or editing in a spreadsheet.

---

*Additional sections on Jupyter Notebooks, APIs, Python packages, file paths, Markdown, and Git/GitHub will be added to this reference guide.*
