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

The libraries you need to install are for this tutorial are:

- `pandas`: helps us easily create, manage, and manipulate structured datasets like our gazetteer of historical places
- `requests`: allows us to send HTTP requests to the GeoNames API, so we can enrich our dataset with location details and additional information from this online geographic database

You can install those libraries by running the following code:
```!pip install pandas requests```

## 4. Load the Input CSV

You can download the sample input file used for this tutorial using this [link](https://github.com/dhp-toolkit/dhp-toolkit.github.io/blob/master/assets/trucial_towns.csv.zip){:target="_blank" rel="noopener"}

If you choose to use your own input file with this tutorial, make sure it's named as `trucial_towns.csv` and has at least the following column:
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
input_file = "trucial_towns.csv"  # Make sure this file is in the same folder as your notebook
df = pd.read_csv(input_file)

# Display the data
df.head()
```

After you run the above code, you will see the following dataframe printed:
![Output](/assets/images/geonames/tutorial-geonames-4.1.png)


## 5. Define the GeoNames Query Function

We will use the free **GeoNames API** to retrieve information for each town. You must register at [https://www.geonames.org/login](https://www.geonames.org/login){:target="_blank" rel="noopener"} and get a username.

`query_geonames` used below is a function that sends a request to the GeoNames API using the place name. It returns information like latitude, longitude, and geographic classification for the most relevant result found.

**Code:**

```ruby
import requests # This library allows us to send HTTP requests (to talk to web APIs)
import time # This library lets us pause between API calls, to avoid rate limits

GEONAMES_USERNAME = "GeoNames_username"  # CHANGE THIS to your registered GeoNames username

def query_geonames(place_name):
    base_url = "http://api.geonames.org/searchJSON" # The GeoNames API endpoint
    params = {
        'q': place_name, # The place name you want to search for (e.g., "Sharjah")
        'maxRows': 1,    # Only return the top 1 result (most relevant match)
        'username': GEONAMES_USERNAME
    }
    response = requests.get(base_url, params=params) # Send GET request to GeoNames
    
    # Print the request info for debugging. This helps you verify whether the request worked.
    print(f"Querying: {place_name}")
    print(f"Status Code: {response.status_code}") 
    if response.status_code == 200: # A status code of 200 means success.
        results = response.json()   # Convert the API response to a Python dictionary
        print(f"Response JSON: {results}") # This will show the full API response
        
        geonames_data = results.get('geonames', [])  # Get the list of results
        if geonames_data:
            top = geonames_data[0]  # Take the first (best match)
            return top.get('lat'), top.get('lng'), top.get('geonameId'), top.get('fcl'), top.get('fcode')
    return '', '', '', '', ''
```


## 6. Query GeoNames for Each Place and Enrich the Dataset

For each row in our dataset, we take the name of the place and use the `query_geonames()` function to retrieve data from GeoNames. The results are added as new columns to the dataset.

We also include a one-second pause between requests (`time.sleep(1)`) to avoid hitting the API's rate limits.

**Code:**

```ruby
# Add empty columns for enriched data
df['latitude'] = ''
df['longitude'] = ''
df['geonames_id'] = ''
df['feature_class'] = ''
df['feature_code'] = ''

# Query each place
for idx, row in df.iterrows():
    lat, lon, gid, fcl, fcode = query_geonames(row['name']) # Call the query_geonames function for each row listed in the dataframe
    df.at[idx, 'latitude'] = lat
    df.at[idx, 'longitude'] = lon
    df.at[idx, 'geonames_id'] = gid
    df.at[idx, 'feature_class'] = fcl
    df.at[idx, 'feature_code'] = fcode
    time.sleep(1)  # Pause to avoid rate limits

# Show enriched data
df.head() # displays the first 5 rows
```

**Expected output:**

Full API Response:
![API Response](/assets/images/geonames/tutorial-geonames-6.1.png)

Final dataframe:
![API Response](/assets/images/geonames/tutorial-geonames-6.2.png)

## 7. Save the Result into a CSV file

**Code:**

```ruby
output_file = "trucial_towns_enriched.csv"
df.to_csv(output_file, index=False) # Save the output as a csv file
print(f"Saved enriched dataset to {output_file}")
```
**Expected output:**

![Output](/assets/images/geonames/tutorial-geonames-7.1.png)


## 8. What Next?

Now that you have coordinates and metadata, you can:
- Import into **QGIS** or **Google My Maps** for visualization
- Align with the **World Historical Gazetteer (WHG)**
- Add temporal information and submit your dataset to WHG for educational use

You can also extend this notebook to:
- Query for multiple alternate names
- Visualize the towns using **Python libraries** like `folium` or `plotly`
- Compare coverage with **Wikidata** or **WHG**

Let your dataset grow from here!
