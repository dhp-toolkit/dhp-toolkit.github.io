---
title: "From AI Transcription to Structured Historical Data: A Gemini API Workflow"
categories:
  - Intermediate
tags:
  - Generative AI
  - Gemini API
  - Structured Data
  - Historical Data
  - Archives
toc: true
toc_sticky: true
toc_label: "Table of Contents"
---

This tutorial introduces a workflow for converting a **reviewed historical transcription** into structured data with a large language model (LLM). Using the **Gemini API** as a worked example, you will ask the model to extract specific categories of information from a transcription and return them in a consistent, machine-readable structure.

The tutorial follows directly from *Transcribing Archival Images with Multimodal AI: A Gemini API Workflow*, but you do not need to complete that tutorial first. You can either use a reviewed transcription dataset produced in the previous tutorial or use the sample transcription dataset provided here.

> **Why Gemini?** This tutorial uses the Gemini API Free Tier because it provides an accessible way to experiment with AI-assisted structured extraction without requiring a paid API account. The workflow itself is not specific to Gemini. The same general method—define what information you want to extract, send a transcription to a model, request structured output, evaluate the result, and save the data—can be adapted to other AI models and platforms. API syntax, model names, pricing, rate limits, and data-use policies differ across providers.

The central historical-method question is:

> **What happens when an AI model converts historical prose into structured data?**

The goal is not simply to produce a neat dataset. We will examine what the model extracts correctly, what it omits, what it misclassifies, and what it adds that was not explicitly present in the transcription.

**Learning Objectives**

By the end of this tutorial, you will be able to:

* Explain what structured historical data is and how it differs from prose transcription.
* Explain the basic structure of JSON and why it is useful for representing structured data.
* Define a simple schema for extracting information from historical text.
* Use the Gemini API to return structured JSON output.
* Extract people, places, dates and times, organizations or offices, and explicitly stated actions or events.
* Compare AI-generated structured data with the original transcription.
* Identify omissions, misclassifications, and information added by the model.
* Process several transcriptions using the same extraction workflow.
* Save structured results as JSON and CSV.
* Explain why evaluation strategies need to change as a dataset grows.

**Prerequisites**

This tutorial assumes basic familiarity with Jupyter Notebook and simple Python code. Familiarity with the previous Gemini transcription tutorial is helpful but not required.

You do **not** need previous experience with JSON, Pydantic, or structured outputs.

> **Last reviewed:** October 2026

<div class="notice--info" markdown="1">

**Use the companion Jupyter Notebook**

You can use the notebook in three ways:

[**Open in Google Colab**](https://colab.research.google.com/github/dhp-toolkit/dhp-toolkit.github.io/blob/master/assets/notebooks/StructuredHistoricalData.ipynb)  
Runs the notebook interactively in your browser. No local Jupyter installation is required.

[**View the notebook on GitHub**](https://github.com/dhp-toolkit/dhp-toolkit.github.io/blob/master/assets/notebooks/StructuredHistoricalData.ipynb)  
Lets you read the notebook and inspect its code in your browser. The cells cannot be run from the GitHub preview.

[**Download the notebook**](https://dhp-toolkit.github.io/assets/notebooks/StructuredHistoricalData.ipynb)  
Download the `.ipynb` file if you want to open it locally in Jupyter Notebook or JupyterLab.

</div>

---

## 1. What Are We Doing in This Tutorial?

In the previous Gemini tutorial, the AI model received an **image** and returned a **transcription**.

In this tutorial, we begin with a **reviewed transcription** and ask Gemini to identify particular types of historical information inside it.

The workflow is:

```text
reviewed historical transcription
            ↓
      extraction instructions
            ↓
        Gemini API
            ↓
     structured JSON data
            ↓
        human review
            ↓
       JSON / CSV dataset
```

A transcription attempts to represent the text of a historical document. Structured extraction reorganizes information from that text into predefined categories.

That process can make historical sources easier to search, compare, count, map, or connect to other datasets. It also introduces another layer of interpretation: the model must decide which pieces of text belong in which categories.

---

## 2. Choose Your Input

You have two options.

### Option A: Continue from the Previous Gemini Tutorial

If you completed *Transcribing Archival Images with Multimodal AI*, use the reviewed `archival_transcriptions.csv` file created there.

In that file:

* each row represents one transcribed archival image or page;
* the `transcription` column contains the transcription text;
* the `image_file` column identifies the source image.

This tutorial can use `image_file` as the record identifier, so you do not need to rename the column.

Use the **reviewed** version of the transcription dataset rather than unchecked model output.

### Option B: Use the Sample Dataset

If you are starting with this tutorial, use the sample dataset provided here:

[**Download the sample transcription dataset**](https://dhp-toolkit.github.io/assets/data/sample_transcriptions.csv)

The sample contains five reviewed page-level transcriptions. Each row represents one archival page.

Its columns are:

```text
record_id
source_file
page_number
transcription
```

The `record_id` is the unique identifier used in this tutorial. `source_file` and `page_number` preserve basic provenance information, while `transcription` contains the reviewed text.

### Why One Row per Page?

The original transcriptions may begin as separate `.txt` files—one file for each page. For this tutorial, they are combined into a single CSV because pandas can then load the whole sample set as one table.

The conversion is conceptually simple:

```text
PAGE 1.txt  → one row
PAGE 2.txt  → one row
PAGE 3.txt  → one row
...
```

Each row keeps the page-level transcription intact.

For the first part of the tutorial, however, we will work with **one transcription at a time** so that we can see exactly what information the model extracts.

---

## 3. What Do We Mean by Structured Historical Data?

Historical sources usually contain information in prose.

Imagine that a transcription contains the following sentence:

```text
On 14 March 1908 Sheikh Mubarak travelled from Kuwait to Basra and reported to the Political Resident.
```

A historian can read the sentence and recognize several kinds of information:

* a person: Sheikh Mubarak;
* places: Kuwait and Basra;
* a date: 14 March 1908;
* an office: Political Resident;
* actions: Sheikh Mubarak travelled from Kuwait to Basra and reported to the Political Resident.

A structured representation might look like this:

```json
{
  "people": [
    "Sheikh Mubarak"
  ],
  "places": [
    "Kuwait",
    "Basra"
  ],
  "dates_times": [
    "14 March 1908"
  ],
  "organizations_offices": [
    "Political Resident"
  ],
  "actions_events": [
    "Sheikh Mubarak travelled from Kuwait to Basra",
    "Sheikh Mubarak reported to the Political Resident"
  ]
}
```

The information is now organized into named categories that a computer can process consistently.

### 3.1 What Is JSON?

The format above is called **JSON**, or JavaScript Object Notation.

JSON is a common way of storing structured information. Despite its name, it is widely used outside JavaScript.

For this tutorial, you only need to recognize two basic elements.

A **key** names a category:

```json
"places"
```

A **list**, shown inside square brackets `[ ]`, contains the values belonging to that category:

```json
"places": [
  "Kuwait",
  "Basra"
]
```

This is useful for historical data because one document might contain no places, one place, or several places.

You do not need to learn JSON syntax in detail to complete this tutorial. Our Python code will create and save the JSON for us.

---

## 4. What Will We Extract?

We will use the same five categories throughout the tutorial:

1. **People**
2. **Places**
3. **Dates and times**
4. **Organizations / offices**
5. **Actions / events explicitly stated in the text**

The categories are deliberately limited. A larger schema could include document type, themes, relationships, occupations, or many other classifications, but every additional category introduces new interpretive decisions.

For this exercise, our main rule is:

> **Extract only information explicitly present in the transcription. Do not use external knowledge to expand, modernize, or complete the information.**

The purpose is to structure information from the supplied historical text, not to enrich or correct it.

---

## 5. Why Use Structured Output?

We could simply ask Gemini:

```text
Find the people, places, and dates in this transcription.
```

The model might respond with a paragraph, bullet points, a table, or another format.

That creates a problem when we want to repeat the task across many documents. If one response is a paragraph, another is a bullet list, and another is a table, Python cannot reliably combine them into a consistent dataset.

Instead, we will tell Gemini exactly what **structure** its response should follow.

For this tutorial, every response should contain:

```text
people
places
dates_times
organizations_offices
actions_events
```

even when one of those categories is empty.

Because every record follows the same structure, Python can process the results systematically and combine them into a larger dataset.

> **Important:** Structured output controls the **format** of the response. It does not guarantee that the historical information inside that structure is correct.

---

## 6. Set Up the Python Environment

```python
%pip install -U google-genai pydantic pandas
```

Now import the tools we need:

```python
from google import genai
from getpass import getpass
from pydantic import BaseModel, Field
from datetime import datetime, timezone
from pathlib import Path
import json
import pandas as pd
```

---

## 7. Connect to the Gemini API

```python
GEMINI_API_KEY = getpass("Enter your Gemini API key: ")
```

```python
client = genai.Client(api_key=GEMINI_API_KEY)
```

> **Never write an API key directly into a notebook that you plan to save, publish, or upload to GitHub.**

Store the model name in one variable:

```python
MODEL = "gemini-3.8-flash"
```

Later, when the code says:

```python
model=MODEL
```

it means: use the model whose name we stored in the `MODEL` variable.

### Data and Privacy

This tutorial sends historical transcriptions to an external AI service.

Use public or unrestricted material for the exercise. Do not submit confidential, restricted, personally sensitive, or otherwise non-shareable research material without checking the provider's terms and the rules governing the collection.

---

## 8. Start with One Transcription

Before processing the full dataset, we will test the workflow on a single transcription.

If you are using the sample CSV, we will load the first record after the file is loaded in Section 14. If you are using your own reviewed transcription, you can also paste one directly:

```python
TRANSCRIPTION = """
[PASTE ONE REVIEWED TRANSCRIPTION HERE]
"""
```

Starting with one record lets us inspect the output closely before automating the same operation across several pages.

---

## 9. Define the Structure We Want

```python
class HistoricalExtraction(BaseModel):
    people: list[str] = Field(
        description="People explicitly named in the transcription."
    )

    places: list[str] = Field(
        description="Places explicitly named in the transcription."
    )

    dates_times: list[str] = Field(
        description="Dates and times explicitly stated in the transcription."
    )

    organizations_offices: list[str] = Field(
        description="Organizations or offices explicitly named in the transcription."
    )

    actions_events: list[str] = Field(
        description="Actions or events explicitly stated in the transcription."
    )
```

A **schema** describes the structure that our output should have. Each field above contains a list of text values.

---

## 10. Write the Extraction Instructions

```python
EXTRACTION_PROMPT = """
Extract structured historical information from the transcription below.

Return only information explicitly present in the transcription.

Use these categories:
- people
- places
- dates and times
- organizations or offices
- actions or events explicitly stated in the text

Rules:
1. Do not add information from outside the transcription.
2. Do not expand names using outside knowledge.
3. Do not modernize historical place names.
4. Do not infer missing dates, identities, motives, causes, or relationships.
5. Preserve the wording of names and places as it appears in the transcription.
6. If a category contains no information, return an empty list.
"""
```

The schema tells Gemini **what fields to return**. The prompt tells Gemini **how to decide what belongs in those fields**.

---

## 11. Ask Gemini for Structured Output

```python
interaction = client.interactions.create(
    model=MODEL,
    input=f"""
{EXTRACTION_PROMPT}

TRANSCRIPTION:
{TRANSCRIPTION}
""",
    response_format={
        "type": "text",
        "mime_type": "application/json",
        "schema": HistoricalExtraction.model_json_schema()
    },
)
```

Validate and display the response:

```python
extraction = HistoricalExtraction.model_validate_json(
    interaction.output_text
)

print(
    extraction.model_dump_json(indent=2)
)
```

---

## 12. Your Task: Compare the Extraction with the Transcription

Compare Gemini's output directly with the reviewed transcription.

Look for four types of outcome:

| Outcome | Meaning |
| --- | --- |
| **Correct** | The item appears in the transcription and is placed in an appropriate category. |
| **Omitted** | Information present in the transcription was not extracted. |
| **Misclassified** | Information was extracted but placed in the wrong category. |
| **Added by the model** | The output contains information not explicitly present in the transcription. |

Record observations addressing these questions:

1. Was anything important omitted?
2. Was anything placed in the wrong category?
3. Did the model normalize or expand anything?
4. Did the model add anything not explicitly present?
5. Which category was easiest to extract accurately?
6. Which category required the most interpretation?

---

## 13. Create a Reusable Extraction Function

```python
def extract_historical_data(transcription, model=MODEL):

    interaction = client.interactions.create(
        model=model,
        input=f"""
{EXTRACTION_PROMPT}

TRANSCRIPTION:
{transcription}
""",
        response_format={
            "type": "text",
            "mime_type": "application/json",
            "schema": HistoricalExtraction.model_json_schema()
        },
    )

    return HistoricalExtraction.model_validate_json(
        interaction.output_text
    )
```

---

## 14. Load a Transcription Dataset

Choose the file you are using.

### Sample Dataset

If you downloaded the Toolkit sample:

```python
DATA_PATH = Path("sample_transcriptions.csv")
```

### Dataset from the Previous Gemini Tutorial

If you are continuing from that tutorial:

```python
DATA_PATH = Path("archival_transcriptions.csv")
```

In Google Colab, upload the CSV using the **Files** panel first, then use the appropriate `/content/...` path.

Load the file:

```python
transcriptions_df = pd.read_csv(DATA_PATH)

transcriptions_df
```

The code below accepts either `record_id` or `image_file` as the identifier:

```python
if "record_id" in transcriptions_df.columns:
    ID_COLUMN = "record_id"
elif "image_file" in transcriptions_df.columns:
    ID_COLUMN = "image_file"
else:
    raise ValueError(
        "The dataset must contain either 'record_id' or 'image_file'."
    )

if "transcription" not in transcriptions_df.columns:
    raise ValueError(
        "The dataset must contain a 'transcription' column."
    )

print(f"Using '{ID_COLUMN}' as the record identifier.")
```

If you have not already tested a transcription manually, you can now use the first row:

```python
TRANSCRIPTION = transcriptions_df.loc[0, "transcription"]

print(TRANSCRIPTION)
```

Then return to Section 11 and run the extraction on this single record before continuing to the batch step.

---

## 15. Process Several Transcriptions

```python
batch_results = []
```

```python
for _, row in transcriptions_df.iterrows():

    print(f"Processing: {row[ID_COLUMN]}")

    try:
        extraction = extract_historical_data(
            row["transcription"]
        )

        result = extraction.model_dump()

        result["record_id"] = row[ID_COLUMN]
        result["transcription"] = row["transcription"]
        result["model"] = MODEL
        result["processed_at_utc"] = datetime.now(
            timezone.utc
        ).isoformat()
        result["review_status"] = ""
        result["review_notes"] = ""

        batch_results.append(result)

    except Exception as e:

        batch_results.append({
            "record_id": row[ID_COLUMN],
            "transcription": row["transcription"],
            "people": [],
            "places": [],
            "dates_times": [],
            "organizations_offices": [],
            "actions_events": [],
            "model": MODEL,
            "processed_at_utc": datetime.now(
                timezone.utc
            ).isoformat(),
            "review_status": "API error",
            "review_notes": str(e)
        })
```

`extraction.model_dump()` converts the validated structured output into a standard Python dictionary. We do that here because dictionaries are easy to add information to and easy to save later.

For larger datasets, check the current API rate limits for the model and account you are using and design the processing workflow accordingly.

---

## 16. Save the Full Structured Results as JSON

```python
with open(
    "structured_historical_data.json",
    "w",
    encoding="utf-8"
) as f:

    json.dump(
        batch_results,
        f,
        ensure_ascii=False,
        indent=2
    )

print("Saved structured_historical_data.json")
```

JSON preserves fields containing multiple values as genuine lists.

---

## 17. Save a Spreadsheet-Friendly CSV

```python
csv_rows = []

for result in batch_results:

    csv_rows.append({
        "record_id": result["record_id"],
        "transcription": result["transcription"],
        "people": "; ".join(result["people"]),
        "places": "; ".join(result["places"]),
        "dates_times": "; ".join(
            result["dates_times"]
        ),
        "organizations_offices": "; ".join(
            result["organizations_offices"]
        ),
        "actions_events": "; ".join(
            result["actions_events"]
        ),
        "model": result["model"],
        "processed_at_utc": result[
            "processed_at_utc"
        ],
        "review_status": result[
            "review_status"
        ],
        "review_notes": result[
            "review_notes"
        ]
    })
```

```python
structured_df = pd.DataFrame(csv_rows)

structured_df
```

```python
structured_df.to_csv(
    "structured_historical_data.csv",
    index=False,
    encoding="utf-8-sig"
)

print("Saved structured_historical_data.csv")
```

---

## 18. What Changes When We Scale Up?

With one transcription, we can compare every extracted item directly with the source.

With five records, we can still inspect every result carefully.

At 100, 1,000, or 10,000 documents, exhaustive manual inspection becomes increasingly costly and difficult.

Larger projects therefore need a **quality-control strategy**, which might include:

* manually reviewing a representative sample;
* identifying recurring error types;
* comparing performance across different kinds of documents;
* measuring error rates where appropriate;
* deciding which fields require human verification;
* flagging particularly consequential records for review.

> **Scaling does not eliminate the need for human evaluation. It changes how that evaluation has to be designed.**

---

## 19. Adapting the Workflow to Other AI Models

The code in this tutorial uses the Gemini API, but the larger workflow is not specific to Gemini:

```text
reviewed transcription
        ↓
define extraction categories
        ↓
send text + instructions to an LLM
        ↓
receive structured output
        ↓
human evaluation
        ↓
save structured dataset
```

Other providers may use different Python libraries, model names, authentication methods, structured-output syntax, pricing, rate limits, and data-use policies.

The historical-method decisions remain the same.

---

## 20. What Could You Do Next?

Possible next steps include:

* comparing structured extraction across different AI models;
* comparing AI extraction with named-entity recognition tools such as spaCy;
* normalizing or reconciling historical place names;
* linking extracted places to GeoNames or another gazetteer;
* counting or visualizing recurring people, places, organizations, or events;
* examining how extraction errors propagate into later analysis;
* building retrieval and search workflows over a larger historical corpus.

---

## 21. Further Resources

### Gemini API

* [Gemini API documentation](https://ai.google.dev/gemini-api/docs)
* [Getting started with the Gemini API](https://ai.google.dev/gemini-api/docs/get-started)
* [Structured outputs](https://ai.google.dev/gemini-api/docs/structured-output)
* [Gemini models](https://ai.google.dev/gemini-api/docs/models)
* [Gemini API pricing](https://ai.google.dev/gemini-api/docs/pricing)
* [Gemini API rate limits](https://ai.google.dev/gemini-api/docs/rate-limits)

### Related Toolkit Tutorials

* *Transcribing Archival Images with Multimodal AI: A Gemini API Workflow*
* *Getting Started with Jupyter Notebook*
* *Trucial Coast Towns: Building a Historical Gazetteer Dataset with GeoNames*
* *Named Entity Recognition with spaCy*
