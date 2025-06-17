---
title: "Trucial Coast Towns: Building a Historical Gazetteer Dataset with GeoNames"
categories:
  - Intermediate
tags:
  - chat
  - Post Formats
toc: true
toc_sticky: true
toc_label: "Table of Contents"
---

## 1. Introduction

"Gazetteers" are structured lists of place names with information about their locations, variants, and historical context. In this tutorial, we will create a small dataset of historical places from the Trucial Coast (pre-UAE region) and enrich it using the GeoNames API.

## 2. Objective
We will:
- Start from a CSV file containing a few historical town names
- Use the GeoNames API to retrieve coordinates and metadata
- Save the enriched dataset to a new CSV file


## 3. Setup: Install Required Libraries

The libraries we would need to install are:

- `pandas`: helps us easily create, manage, and manipulate structured datasets like our gazetteer of historical places
- `requests`": allows us to send HTTP requests to the GeoNames API, so we can enrich our dataset with location details and additional information from this online geographic database

You can install those libraries by running the following code:
```!pip install pandas requests```

## 4. Load the Input CSV

You can download the sample input file used for this tutorial using this [link](https://github.com/dhp-toolkit/dhp-toolkit.github.io/blob/8696c45d497e404bad7fdfb66a07d609571181d8/assets/sample-archive.zip) 

If you choose to use your own input file with this tutorial, make sure it's named as `trucial_towns.csv` and has at least the following column:
- `name`: historical place name


Aditional columns (optional but recommended):
- `type`: A description of the kind of place — e.g., fort, settlement, port, etc.
- `source`: A note about where the name came from — e.g., a historical map, archive, or article

Columns `source, type` won’t be used in the GeoNames query itself, but they are useful for organizing, filtering, or analyzing your results later.

**Example row:**
```
Ras Al Khaimah,	port, Lorimer Gazetteer
```
> 📌 Note: In this tutorial, you do not need to provide a country code to make a query. The GeoNames API will search globally using only the place name.

