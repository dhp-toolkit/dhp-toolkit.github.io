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

A hands-on, beginner-friendly guide to querying historical place data using the GeoNames API. Learn how to search for places by name, extract geographic coordinates, feature types, and alternate names, and save the results for analysis—all through clear Python examples. No prior experience with APIs required; perfect for students and researchers in history or the humanities working with historical maps, gazetteers, or place-based data.

**Prerequisites:**

This tutorial assumes a basic understanding of how to work with Jupyter Notebook. We strongly recommend that you take the `Getting Started with Jupyter Notebook` tutorial before taking this tutorial.

**Before you begin:**

You can follow this tutorial in one of two ways:

- Download and run the notebook directly on your computer using Jupyter (e.g., through Anaconda or any Python environment that supports notebooks), or
- Create a new Jupyter notebook and copy-paste the code and explanations step-by-step as you follow along. This lets you write and test your own code interactively.

Choose whichever method you’re most comfortable with — both will work just fine!

The **ready-to-run** Jupyter notebook can be downloaded following this [link](https://github.com/dhp-toolkit/dhp-toolkit.github.io/blob/master/assets/notebooks/QueryingGeoNames.ipynb){:target="_blank" rel="noopener"}. 

> To download the file following the above link, click on the 3 dots on the upper-right corner which displays **'More file actions'**, then click on the **Download** button.
![Screenshot of the download button](/assets/images/geonames/tutorial-geonames-1.1.png)


## 1. Introduction

**"Gazetteers"** are structured lists of place names with information about their locations, variants, and historical context. In this tutorial, we will create a small dataset of historical places from the **Trucial Coast** (pre-UAE region) and enrich it using the **GeoNames API**.

## 2. Objective
In this tutorial you will learn how to:
- Start from a **CSV** file containing a few historical town names
- Use the **GeoNames API** to retrieve coordinates and metadata
- Save the enriched dataset to a new **CSV** file


## 3. Install Required Libraries

The libraries you need to install for this tutorial are:

- **`pandas`**: helps us easily create, manage, and manipulate structured datasets like our gazetteer of historical places
- **`requests`**: allows us to send HTTP requests to the GeoNames API, so we can enrich our dataset with location adetails and additional information from this online geographic database

> **Note:** These libraries might already be installed in your environment. In that case, running the command will display a message similar to:`Requirement already satisfied`

You can install those libraries by running the following code:
```!pip install pandas requests```

## 4. Load the Input CSV

You can download the sample input file used for this tutorial using this [link](https://github.com/dhp-toolkit/dhp-toolkit.github.io/blob/master/assets/trucial_towns.zip){:target="_blank" rel="noopener"}.

If you choose to use your own input file with this tutorial, be sure to replace the file name in the code (`trucial_towns.csv`) with the name of your own file and ensure your file contains at least the following column:
- `name`: historical place name

Aditional columns (optional but recommended):
- `type`: A description of the kind of place — e.g., fort, settlement, port, etc.
- `source`: A note about where the name came from — e.g., a historical map, archive, or article

Columns `source` and `type` won’t be used in the GeoNames query itself, but they are useful for organizing, filtering, or analyzing your results later.

**Example row:**

```ruby
Ras Al Khaimah,	port, Lorimer Gazetteer
```
> 📌 Note: In this tutorial, you do not need to provide a country code to make a query. The GeoNames API will search globally using only the place name.


**Loading the CSV:**

Make sure to save `trucial_towns.csv` in the same folder as this notebook file (`.ipynb`) so it can be loaded correctly.

```ruby
import pandas as pd

# Load input CSV
input_file = "trucial_towns.csv"  # Rename this if to match your file name and make sure it is in the same folder as your notebook
df = pd.read_csv(input_file)

# Display the data
df.head()
```

After you run the above code, you will see the following dataframe printed:
![Output](/assets/images/geonames/tutorial-geonames-4.1.png)


## 5. Define the GeoNames Query Function

We will use the free **GeoNames API** to retrieve information for each town. You must register at [https://www.geonames.org/login](https://www.geonames.org/login){:target="_blank" rel="noopener"} and get a username.

To enrich the dataset, the tutorial leverages the free GeoNames API. Before proceeding, you need to register on the GeoNames website [https://www.geonames.org/login](https://www.geonames.org/login) to obtain a username.

The following code defines the `query_geonames` function which is designed to send a request to the GeoNames API using a given place name. It processes the API's JSON response and extracts relevant information such as latitude, longitude, GeoNames ID, feature class, feature code, and filtered alternate names. The function includes logic to filter out irrelevant alternate names, such as URLs, airport codes (IATA, ICAO, FAAC), postal codes, Wikidata IDs, and short, all-caps codes. 

This function constructs the API request, sends it, and then parses the JSON response to extract and return the desired geographic data and a semicolon-separated string of filtered alternate names. Error handling for requests exceptions is also included.

Remember to replace `yourGeonamesUsername` with your actual GeoNames username.

**Code:**

```ruby
import requests # For making HTTP requests to web services.
import time     # For pausing execution to avoid hitting API rate limits.

GEONAMES_USERNAME = "yourGeonamesUsername"  # CHANGE THIS with your GeoNames username

def query_geonames(place_name): # Function to query the GeoNames API for a given place name.
    base_url = "http://api.geonames.org/searchJSON" # Base URL for GeoNames search API.
    params = {
        'q': place_name,    # Query parameter: the place name to search.
        'maxRows': 1,       # Limit results to the top 1 match.
        'username': GEONAMES_USERNAME, # Your GeoNames username.
        'style': 'FULL'     # Request full details in the response.
    }
    
    try:
        response = requests.get(base_url, params=params) # Send GET request.
        response.raise_for_status()                     # Raise an exception for bad status codes (4xx or 5xx).
        
        print(f"Querying: {place_name}")               # Debug print: shows current query.
        print(f"Status Code: {response.status_code}") # Debug print: shows HTTP status.
        
        results = response.json()                       # Parse JSON response into a Python dictionary.
        # print(f"Response JSON: {results}")             # Debug print: shows full API response.
        geonames_data = results.get('geonames', [])     # Extract 'geonames' list, default to empty list if not found.
        
        if geonames_data:
            top = geonames_data[0]                      # Get the first (and only) result.
            
            filtered_alt_names = []                     # Initialize list for cleaned alternate names.
            if 'alternateNames' in top:
                for alt_obj in top['alternateNames']:
                    name = alt_obj.get('name', '')      # Get alternate name.
                    lang = alt_obj.get('lang', '')      # Get language code.

                    # Filter rules for alternate names:
                    if name.startswith('http'): continue          # Exclude URLs.
                    if lang in ['iata', 'icao', 'faac', 'post', 'wkdt']: continue # Exclude specific codes.
                    if len(name) <= 5 and name.isupper(): continue # Exclude short, all-caps codes.
                    if name.startswith('Q') and name[1:].isdigit(): continue # Exclude Wikidata Q-codes.

                    filtered_alt_names.append(name)     # Add valid alternate name.
            
            unique_alt_names = list(dict.fromkeys(filtered_alt_names)) # Remove duplicates while preserving order.
            alt_names_str = "; ".join(unique_alt_names) # Join unique names with semicolon.
            
            return (top.get('lat'),                     # Return latitude.
                    top.get('lng'),                     # Return longitude.
                    top.get('geonameId'),               # Return GeoNames ID.
                    top.get('fcl'),                     # Return feature class.
                    top.get('fcode'),                   # Return feature code.
                    alt_names_str)                      # Return filtered alternate names string.

    except requests.exceptions.RequestException as e:
        print(f"Error querying for '{place_name}': {e}") # Print error message for request failures.
        
    return '', '', '', '', '', '' # Return empty strings on error or no results.
```


## 6. Enrich the Dataset

This section iterates through each row of the loaded dataset (`df`). For each historical place name, it calls the `query_geonames()` function to fetch relevant geographical data from the GeoNames API. 

The retrieved data, including latitude, longitude, GeoNames ID, feature class, feature code, and alternate names, is then added as new columns to the DataFrame. 

A one-second pause (**`time.sleep(1)`**) is incorporated between API requests to prevent exceeding GeoNames' rate limits. 
After processing all entries, the updated DataFrame with the newly added information is displayed.
For each row in our dataset, we take the name of the place and use the `query_geonames()` function to retrieve data from GeoNames. The results are added as new columns to the dataset.

**Code:**

```ruby
df['latitude'] = ''
df['longitude'] = ''
df['geonames_id'] = ''
df['feature_class'] = ''
df['feature_code'] = ''
df['alternate_names'] = ''

# Query each place
for idx, row in df.iterrows():
    # The function now returns the alternate names string directly
    lat, lon, gid, fcl, fcode, alt_names = query_geonames(row['name']) 
    
    df.at[idx, 'latitude'] = lat
    df.at[idx, 'longitude'] = lon
    df.at[idx, 'geonames_id'] = gid
    df.at[idx, 'feature_class'] = fcl
    df.at[idx, 'feature_code'] = fcode
    df.at[idx, 'alternate_names'] = alt_names # Populate the new column
    
    time.sleep(1)  # Pause to avoid rate limits

# Show the first five entries of the table
print(df.head())
```

**Expected output:**

![Output](/assets/images/geonames/tutorial-geonames-6.1.png)


## 7. Save the Result into a CSV file

After the dataset has been enriched with information from GeoNames, the next step is to save the updated DataFrame to a new CSV file. This ensures that the retrieved data is persistently stored and can be used for further analysis or visualization.

The following code block saves the `df` DataFrame to a new CSV file named **`trucial_towns_enriched.csv`**. 
The `index=False` argument prevents pandas from writing the DataFrame index as a column in the CSV file. 
A confirmation message is then printed to indicate that the file has been saved.

**Code:**

```ruby
output_file = "trucial_towns_enriched.csv"
df.to_csv(output_file, index=False) # Save the output as a csv file
print(f"Saved enriched dataset to {output_file}")
```
**Expected output:**

![Output](/assets/images/geonames/tutorial-geonames-7.1.png)



## 8. Querying by Country, Feature Class, and Feature Code

Sometimes, you might want to find specific types of geographical features within a particular country, rather than searching for a named place globally. The GeoNames API allows you to refine your search using parameters like: 
- **`country`** (two-letter ISO country code)
- **`featureClass`**` (a broad category like 'H' for hydrographic features or 'P' for populated places)
- **`featureCode`**` (a more specific type within a feature class, like 'RVR' for river or 'LAKE' for lake).

You can get the list of featureCodes that belong to each featureClass [here](https://www.geonames.org/export/codes.html).

This section demonstrates how to query for all hydrographic features (rivers, lakes, etc.) within a specific country (e.g., Turkey) and then save these results to a CSV file.

First, let's define a new function **`query_geonames_by_criteria()`** that takes `country_code`, `feature_class`, and an optional `feature_code` as arguments. This function will fetch results based on these criteria.

**Code:**

```ruby

import requests
import pandas as pd # Import pandas to work with DataFrames and save to CSV

GEONAMES_USERNAME = "yourGeonamesUsername"  # Replace with your actual GeoNames username

def query_geonames_by_criteria(country_code, feature_class, feature_code=None, max_rows=1000):
    # This function queries GeoNames for features based on country, feature class, and optional feature code
    url = "http://api.geonames.org/searchJSON"
    params = {
        'country': country_code,     # ISO 2-letter country code (e.g., 'TR' for Turkey)
        'featureClass': feature_class, # Feature Class (e.g., 'H' for hydrographic, 'P' for populated place)
        'maxRows': max_rows,         # Maximum number of results to retrieve (up to 1000 for free account)
        'username': GEONAMES_USERNAME
    }
    
    if feature_code:
        params['featureCode'] = feature_code # Add featureCode to parameters if provided.

    print(f"Querying GeoNames for features in {country_code} (Class: {feature_class}, Code: {feature_code if feature_code else 'Any'})...")
    
    try:
        response = requests.get(url, params=params) # Send GET request
        response.raise_for_status() # Raise an exception for HTTP errors
        
        print(f"Status Code: {response.status_code}") # Print HTTP status
        data = response.json() # Parse JSON response
        results = data.get('geonames', []) # Extract 'geonames' list
        
        print(f"Found {len(results)} features.") # Print number of results
        
        # Optionally print a sample of the results
        for i, place in enumerate(results[:5]):
            print(f"  Sample {i+1}: Name: {place.get('name')}, Code: {place.get('fcode')}, Lat: {place.get('lat')}, Lng: {place.get('lng')}")
            
        return results
        
    except requests.exceptions.RequestException as e: # Catch errors as exceptions
        print(f"Error querying GeoNames: {e}") # Print error message
        return []

# Example Usage: Query for hydrographic features in Turkey
print("\n--- Querying Hydrographic Features in Turkey ---")
tr_hydro_features = query_geonames_by_criteria(country_code='TR', feature_class='H')

# Example Usage: Query for specific feature code - Rivers (RVR) in Turkey
print("\n--- Querying Rivers (RVR) in Turkey ---")
tr_lakes= query_geonames_by_criteria(country_code='TR', feature_class='H', feature_code='LK')

```

**Expected output:**

![Output](/assets/images/geonames/tutorial-geonames-8.1.png)


Now that we have functions to query based on specific criteria, let's save the results into a CSV file for further use. We'll convert the list of dictionaries returned by the function into a Pandas DataFrame and then save it.

**Code:**

```ruby
# Define the desired column order
desired_columns = ['name', 'lat', 'lng', 'countryName', 'countryCode', 'fcl', 'fclName', 'fcode', 'fcodeName', 'adminName1']

# Process and save hydrographic features for Turkey
if tr_hydro_features:
    df_tr_hydro = pd.DataFrame(tr_hydro_features) # Convert results to DataFrame
    df_tr_hydro_selected = df_tr_hydro[desired_columns]     # Select and reorder columns
    output_filename_hydro_tr = "tr_hydrographic_features.csv" # Define output filename
    df_tr_hydro_selected.to_csv(output_filename_hydro_tr, index=False) # Save to CSV, no index
    print(f"\nSaved {len(tr_hydro_features)} hydrographic features from Turkey to {output_filename_hydro_tr} with specified columns.")

# Process and save rivers for Turkey
if tr_rivers:
    df_tr_rivers = pd.DataFrame(tr_rivers)
    df_tr_rivers_selected = df_tr_rivers[desired_columns]
    output_filename_rivers_tr = "tr_rivers.csv"
    df_tr_rivers_selected.to_csv(output_filename_rivers_tr, index=False)
    print(f"Saved {len(tr_rivers)} rivers from Turkey to {output_filename_rivers_tr} with specified columns.")

```
**Expected output:**

![Output](/assets/images/geonames/tutorial-geonames-8.2.png)



## 9. What's Next?

Now that you have coordinates and metadata, you can try:
- Importing into **QGIS** or **Google My Maps** for visualization
- Aligning with the **World Historical Gazetteer (WHG)**

You can also extend this notebook to:
- Query for multiple alternate names
- Visualize the towns using **Python libraries** like `folium` or `plotly`
- Compare coverage with **Wikidata** or **WHG**
