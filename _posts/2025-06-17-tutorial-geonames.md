---
title: "Trucial Coast Towns: Building a Historical Gazetteer Dataset with GeoNames"
categories:
  - Intermediate
tags:
  - GeoNames
  - APIs
  - Gazetteers
toc: true
toc_sticky: true
toc_label: "Table of Contents"
---

A hands-on guide to using the GeoNames API to build and review a historical gazetteer. Using place names from the Trucial Coast as an example, you will learn how to query an online geographic database, retrieve candidate matches and geographic metadata, evaluate ambiguous results, and save a reviewed dataset for further historical analysis or mapping.

**Learning Objectives:**

By the end of this tutorial, you will be able to:

* Explain what a gazetteer is and how it can support historical research.
* Send queries to the GeoNames API from a Jupyter Notebook.
* Retrieve coordinates, feature types, administrative information, and alternate place names.
* Compare multiple candidate matches rather than automatically accepting the first result.
* Use historical evidence to evaluate and document place-name matches.
* Save both candidate results and a reviewed gazetteer as CSV files.
* Refine GeoNames searches using country, feature class, and feature code filters.

**Prerequisites:**

This tutorial assumes basic familiarity with Jupyter Notebook and simple Python code. If you are new to notebooks, we recommend completing the *Getting Started with Jupyter Notebook* tutorial first.

> **Last reviewed:** September 2026

---

## 1. Introduction

A **gazetteer** is a structured collection of place names linked to information such as coordinates, alternate names, administrative areas, or feature types. Gazetteers can help historians connect place names found in maps, newspapers, archival records, travel accounts, and other sources to geographic locations.

In this tutorial, we will work with a small collection of historical place names from the **Trucial Coast**, the British-era designation associated with the sheikhdoms that later formed the United Arab Emirates. We will use the **GeoNames API** to retrieve geographic information for these names.

The goal, however, is not simply to convert names into coordinates automatically.

### 1.1 A Historical Caution

GeoNames is a large contemporary geographic database. It is useful for historical research, but it is **not itself a historical authority**.

A historical place name may:

* have several possible modern equivalents,
* appear under multiple spellings or transliterations,
* refer to a settlement whose boundaries or status have changed,
* have the same name as places elsewhere,
* be absent from GeoNames altogether,
* be classified according to a modern geographic or administrative system that did not exist in the historical period you are studying.

For this reason, a GeoNames search result should initially be treated as a **candidate match**, not automatically as the correct identification.

In this tutorial, we will therefore retrieve several candidates for each historical place name and review them before creating the final gazetteer.

---

## 2. Before You Begin

You can follow this tutorial by creating a new Jupyter Notebook and copying the code and explanations step by step.

You will also need the sample dataset:

[Download the sample Trucial Coast towns dataset](/assets/trucial_towns.zip)

After downloading the ZIP file, extract it and place the file ```trucial_towns.csv``` in the same project folder as your Jupyter Notebook.

Your project folder might look like this:

```text
geonames-project/
├── geonames_tutorial.ipynb
└── trucial_towns.csv
```

Keeping the notebook and data together makes the file paths in this tutorial easier to follow.


If you prefer to work from a ready-to-run notebook, you can download the companion Jupyter Notebook here:

[Download `QueryingGeoNames.ipynb`](/assets/notebooks/QueryingGeoNames.ipynb)

You can either run the notebook directly or use it alongside the step-by-step explanations below.

---

## 3. Install Required Libraries

We will use two Python libraries:

* **pandas** for working with tabular data.
* **requests** for communicating with the GeoNames web service.

Inside a Jupyter Notebook, run:

```python
%pip install pandas requests
```

> **Note:** If the libraries are already installed in the Python environment used by your notebook, you may see a message such as ```Requirement already satisfied```.

Now import the libraries we will use:

```python
import pandas as pd
import requests
import time
```

---

## 4. Load the Historical Place-Name Dataset

The sample CSV contains historical place names and contextual information.

At minimum, your own dataset should contain a column called:

```text
name
```

This is the place name that will be sent to GeoNames.

Additional columns are strongly recommended. For example:

* ```type``` — the type of place described by your historical source, such as port, settlement, fort, or village.
* ```source``` — where the historical name came from, such as a map, gazetteer, archival document, or newspaper.

These columns are important because historical context can help you determine whether a candidate returned by GeoNames is actually the place mentioned in your source.

For example:

```text
name,type,source
Ras Al Khaimah,port,Lorimer Gazetteer
```

Load the CSV:

```python
input_file = "trucial_towns.csv"

df = pd.read_csv(input_file)

df.head()
```

![Output](/assets/images/geonames/tutorial-geonames-4.1.png)

Before continuing, check that the ```name``` column exists:

```python
if "name" not in df.columns:
    raise ValueError("The input CSV must contain a column named 'name'.")
```

---

## 5. Set Up Access to the GeoNames API

GeoNames provides a web service that allows programs to send geographic queries and receive structured results.

To use the free GeoNames web service:

1. Create a GeoNames account at [GeoNames](https://www.geonames.org/login){:target="_blank" rel="noopener"}.
2. Confirm your account.
3. Enable web-service access from your GeoNames account page.
4. Replace the placeholder below with your GeoNames username.

```python
GEONAMES_USERNAME = "yourGeonamesUsername"
```

Your GeoNames username is sent with each API request.

> **Security note:** A GeoNames username is not the same thing as your password. Never put passwords, API keys, or other private credentials directly into a notebook that you plan to publish.

We will use GeoNames' secure JSON endpoint:

```python
GEONAMES_URL = "https://secure.geonames.org/searchJSON"
```

---

## 6. Query GeoNames for Candidate Matches

### 6.1 Why Retrieve More Than One Result?

A very simple geocoding script might request one result and automatically assume that the first result is correct.

That approach is risky for historical research.

If a historical source contains the name ```Sharjah```, for example, the historian should still examine whether the result's location, feature type, administrative area, and alternate names make sense in relation to the source.

We will therefore retrieve up to **five candidate matches** for each name.

### 6.2 Prepare Alternate Names

When we request detailed results from GeoNames, a record may contain alternate names in different languages and scripts.

The following helper function keeps useful names while removing entries such as web links and some machine-readable codes:

```python
def format_alternate_names(place):
    alternate_names = []

    for alt in place.get("alternateNames", []):
        name = (alt.get("name") or "").strip()
        lang = (alt.get("lang") or "").strip()

        if not name:
            continue

        # Exclude URLs and common non-name identifiers
        if name.startswith("http"):
            continue

        if lang in {"link", "iata", "icao", "faac", "post", "wkdt"}:
            continue

        # Preserve language information when GeoNames provides it
        if lang:
            label = f"{lang}:{name}"
        else:
            label = name

        alternate_names.append(label)

    # Remove duplicates while preserving order
    alternate_names = list(dict.fromkeys(alternate_names))

    return "; ".join(alternate_names)
```

Keeping language information can be especially useful when working with multilingual historical sources.

### 6.3 Define the Query Function

Now define a function that searches GeoNames and returns several possible matches:

```python
def query_geonames_candidates(
    place_name,
    max_rows=5,
    country_bias=None,
    country=None,
    feature_class=None,
    feature_code=None
):
    params = {
        "q": place_name,
        "maxRows": max_rows,
        "username": GEONAMES_USERNAME,
        "style": "FULL"
    }

    # Optional parameters for refining the search
    if country_bias:
        params["countryBias"] = country_bias

    if country:
        params["country"] = country

    if feature_class:
        params["featureClass"] = feature_class

    if feature_code:
        params["featureCode"] = feature_code

    try:
        response = requests.get(
            GEONAMES_URL,
            params=params,
            timeout=30
        )

        response.raise_for_status()

        data = response.json()

        # GeoNames can return an API-level error inside the JSON response
        if "status" in data:
            status = data["status"]
            raise RuntimeError(
                f"GeoNames error {status.get('value')}: "
                f"{status.get('message')}"
            )

        results = data.get("geonames", [])

        candidates = []

        for rank, place in enumerate(results, start=1):
            candidate = {
                "query_name": place_name,
                "candidate_rank": rank,
                "geonames_name": place.get("name"),
                "toponym_name": place.get("toponymName"),
                "latitude": place.get("lat"),
                "longitude": place.get("lng"),
                "geonames_id": place.get("geonameId"),
                "country_name": place.get("countryName"),
                "country_code": place.get("countryCode"),
                "admin_name_1": place.get("adminName1"),
                "feature_class": place.get("fcl"),
                "feature_class_name": place.get("fclName"),
                "feature_code": place.get("fcode"),
                "feature_code_name": place.get("fcodeName"),
                "population": place.get("population"),
                "alternate_names": format_alternate_names(place)
            }

            if place.get("geonameId"):
                candidate["geonames_url"] = (
                    f"https://www.geonames.org/{place.get('geonameId')}"
                )
            else:
                candidate["geonames_url"] = ""

            candidates.append(candidate)

        return candidates

    except requests.exceptions.RequestException as e:
        print(f"Request error for '{place_name}': {e}")
        return []

    except (ValueError, RuntimeError) as e:
        print(f"GeoNames error for '{place_name}': {e}")
        return []
```

### 6.4 Test the Function

Try querying one place:

```python
test_results = query_geonames_candidates(
    "Ras Al Khaimah",
    max_rows=5,
    country_bias="AE"
)

pd.DataFrame(test_results)
```

The parameter:

```python
country_bias="AE"
```

tells GeoNames to give preference to results associated with the United Arab Emirates.

Importantly, a **country bias does not exclude results from other countries**. This is useful when working with historical material because it helps rank likely matches without assuming in advance that every name must fall within a modern national boundary.

If you want to restrict a query strictly to a modern country, use:

```python
country="AE"
```

instead.

---

## 7. Query the Entire Dataset

We can now query each historical place name in the input CSV.

For the Trucial Coast sample, we will use the UAE country code as a **bias**, not as a strict filter.

```python
candidate_rows = []

for idx, row in df.iterrows():
    historical_name = row["name"]

    print(f"Querying: {historical_name}")

    results = query_geonames_candidates(
        historical_name,
        max_rows=5,
        country_bias="AE"
    )

    if results:
        for candidate in results:
            candidate["historical_type"] = row.get("type", "")
            candidate["source"] = row.get("source", "")
            candidate_rows.append(candidate)

    else:
        # Preserve unresolved names rather than silently dropping them
        candidate_rows.append({
            "query_name": historical_name,
            "candidate_rank": "",
            "geonames_name": "",
            "toponym_name": "",
            "latitude": "",
            "longitude": "",
            "geonames_id": "",
            "country_name": "",
            "country_code": "",
            "admin_name_1": "",
            "feature_class": "",
            "feature_class_name": "",
            "feature_code": "",
            "feature_code_name": "",
            "population": "",
            "alternate_names": "",
            "geonames_url": "",
            "historical_type": row.get("type", ""),
            "source": row.get("source", "")
        })

    # A simple pause between requests
    time.sleep(1)

candidates_df = pd.DataFrame(candidate_rows)
```

Now inspect some of the candidate results:

```python
columns_to_view = [
    "query_name",
    "candidate_rank",
    "geonames_name",
    "country_code",
    "admin_name_1",
    "feature_code",
    "latitude",
    "longitude",
    "alternate_names"
]

candidates_df[columns_to_view].head(20)
```

Your table now contains **multiple possible matches for each historical name**, rather than automatically selecting one result.

> **For larger projects:** Check GeoNames' current web-service limits and terms before running a large number of requests. The simple pause used here is suitable for a small teaching dataset but is not a substitute for designing a larger API workflow responsibly.

---

## 8. Review Candidate Matches

This is the most important step for historical work.

For each historical place name, examine the candidate results and ask:

* Does the modern geographic location fit what I know from the historical source?
* Is the feature type plausible?
* Do the alternate names include spellings or transliterations found in my source?
* Does the administrative region make sense?
* Are there several places with similar names?
* Does a historical map or gazetteer provide evidence that helps distinguish between them?
* Has the settlement moved, expanded, disappeared, or changed administrative status?

The **GeoNames ID** is particularly useful because it provides a stable identifier for the GeoNames record.

### 8.1 Add Review Columns

Add three columns for documenting your decision:

```python
candidates_df["selected"] = ""
candidates_df["match_confidence"] = ""
candidates_df["review_notes"] = ""
```

Save the candidate table:

```python
candidate_file = "trucial_towns_candidates.csv"

candidates_df.to_csv(
    candidate_file,
    index=False,
    encoding="utf-8-sig"
)

print(f"Saved candidate matches to {candidate_file}")
```

The ```utf-8-sig``` encoding helps preserve multilingual text, including Arabic-script names, when the CSV is opened in commonly used spreadsheet software.

### 8.2 Review the CSV Manually

Open ```trucial_towns_candidates.csv``` in spreadsheet software.

For the candidate you believe is the best match, enter:

```text
yes
```

in the ```selected``` column.

In ```match_confidence```, you might use:

```text
high
medium
low
```

Use ```review_notes``` to record why you chose the candidate.

For example:

```text
Coordinates and alternate Arabic name match the location shown in the 1908 map.
```

If none of the candidates can be identified confidently, **do not force a match**. Leave ```selected``` blank and record the uncertainty in ```review_notes```.

Save the reviewed file as:

```text
trucial_towns_candidates_reviewed.csv
```

---

## 9. Create the Reviewed Gazetteer

Load the reviewed file:

```python
reviewed_file = "trucial_towns_candidates_reviewed.csv"

reviewed_df = pd.read_csv(reviewed_file)
```

Now keep only rows marked as selected:

```python
selected_values = {"yes", "y", "true", "1"}

selected_df = reviewed_df[
    reviewed_df["selected"]
    .astype(str)
    .str.strip()
    .str.lower()
    .isin(selected_values)
].copy()
```

Inspect the selected records:

```python
selected_df[
    [
        "query_name",
        "geonames_name",
        "latitude",
        "longitude",
        "geonames_id",
        "feature_code",
        "match_confidence",
        "review_notes"
    ]
]
```

Save the reviewed historical gazetteer:

```python
output_file = "trucial_towns_enriched.csv"

selected_df.to_csv(
    output_file,
    index=False,
    encoding="utf-8-sig"
)

print(f"Saved reviewed gazetteer to {output_file}")
```

You now have two useful research objects:

```text
trucial_towns_candidates.csv
```

contains the possible GeoNames matches returned by the API, while:

```text
trucial_towns_enriched.csv
```

contains the matches you selected after historical review.

Keeping both is good research practice because it preserves evidence of the matching process rather than recording only the final decision.

---

## 10. Refining Searches with Country and Feature Types

GeoNames allows you to refine a search using parameters such as:

* ```country``` — a two-letter ISO country code.
* ```countryBias``` — prioritizes results from a country without excluding other countries.
* ```featureClass``` — a broad category of geographic feature.
* ```featureCode``` — a more specific feature type.

GeoNames feature classes include, for example:

* ```P``` — populated places
* ```H``` — streams, lakes, and other hydrographic features
* ```A``` — countries, states, and administrative divisions
* ```S``` — spots, buildings, farms, and other sites

Feature codes provide more specific classifications.

You can consult the GeoNames feature-code list here:

[GeoNames Feature Codes](https://www.geonames.org/export/codes.html){:target="_blank" rel="noopener"}

### 10.1 Restrict a Search to Populated Places

For example, to search for Dubai only among populated places in the UAE:

```python
dubai_candidates = query_geonames_candidates(
    "Dubai",
    max_rows=10,
    country="AE",
    feature_class="P"
)

pd.DataFrame(dubai_candidates)
```

Here, ```country="AE"``` is a strict filter rather than a bias.

### 10.2 Search for a Lake

GeoNames uses:

```text
H
```

for the hydrographic feature class and:

```text
LK
```

for a lake.

For example:

```python
lake_candidates = query_geonames_candidates(
    "Van",
    max_rows=10,
    country="TR",
    feature_class="H",
    feature_code="LK"
)

pd.DataFrame(lake_candidates)
```

### 10.3 Search for a Stream or River

In GeoNames, the feature code:

```text
STM
```

means **stream**, the category used for many rivers.

For example:

```python
river_candidates = query_geonames_candidates(
    "Euphrates",
    max_rows=10,
    country="TR",
    feature_class="H",
    feature_code="STM"
)

pd.DataFrame(river_candidates)
```

Feature classifications are useful for narrowing a search, but remember that they are part of the GeoNames data model. Historical sources may classify or describe places differently.

---

## 11. What Does the API Actually Do?

It is useful to understand what happened computationally.

When you ran:

```python
query_geonames_candidates("Ras Al Khaimah")
```

Python:

1. created a set of query parameters,
2. sent them to the GeoNames web service,
3. received a response in **JSON** format,
4. converted that JSON into Python data structures,
5. extracted selected fields,
6. placed those fields into a pandas DataFrame.

An **API** therefore provides a structured way for one piece of software to request data or functionality from another service.

This is different from manually searching the GeoNames website. Because the query is performed through code, the same operation can be repeated systematically across many records.

That repeatability is useful—but it also makes mistakes scalable. If a script automatically chooses the wrong place for one historical name, applying the same rule to thousands of names can reproduce that mistake across an entire dataset.

This is one reason why documenting candidate selection and uncertainty matters.

---

## 12. Historical Gazetteers and Data Provenance

When creating a gazetteer for historical research, try to distinguish between information that comes from your **historical source** and information supplied by a **modern geographic database**.

For example:

| Field | Possible Source |
| --- | --- |
| Historical place name | Archival document or historical map |
| Historical place type | Historical source |
| Source citation | Your research documentation |
| GeoNames ID | GeoNames |
| Latitude / longitude | GeoNames |
| Modern administrative area | GeoNames |
| Alternate names | GeoNames |
| Match confidence | Researcher |
| Review notes | Researcher |

Maintaining these distinctions makes the provenance of the dataset clearer.

It also allows another researcher—or your future self—to understand which information came from the historical record, which information was added computationally, and which judgments were made during the matching process.

---

## 13. What's Next?

Once you have a reviewed historical gazetteer, you can:

* import the coordinates into **QGIS** for mapping,
* compare your results with the **World Historical Gazetteer (WHG)**,
* compare place identifiers and alternate names with **Wikidata**,
* investigate unmatched or low-confidence places using historical maps and gazetteers,
* visualize the results with Python libraries such as ```folium``` or ```plotly```,
* record multiple historical names for the same place,
* add temporal information indicating when a particular place name or administrative designation was used,
* compare how different gazetteers represent the same historical geography.

For larger historical datasets, place-name matching can become a research problem in its own right. Automated retrieval can help generate possible matches, but the final identification of a historical place often depends on context, source criticism, and explicit documentation of uncertainty.
