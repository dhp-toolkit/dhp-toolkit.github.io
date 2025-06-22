---
title: "Named Entity Recognition With SpaCy"
categories:
  - Advanced
tags:
  - Post Formats
  - readability
  - standard
toc: true
toc_sticky: true
toc_label: "Table of Contents"
---

In this tutorial we will learn how to use **spaCy** to identify place names and people's names from the historical document `AgreementWithTrucialChiefs.txt`.

### Step 1: Install Dependencies

First, you'll need to install **spaCy** and download an English language model. To install them, run the below commands in your terminal:

```ruby
# pip install spacy
# python -m spacy download en_core_web_sm
```


### Step 2: Set Up and Load SpaCy Model

We need to import the necessary libraries and load the pre-trained `spaCy` English language model. This model will be used to identify entities in your text. We'll also import `re` for regular expression operations needed in text cleaning.

### Explanation:

- `import spacy`: Imports the main spaCy library.
- `import re`: Imports the regular expression module, which is very useful for flexible text manipulation.
- `nlp = spacy.load("en_core_web_sm")`: Loads the small English language model. This model includes components for tokenization, POS tagging, dependency parsing, and, importantly, named entity recognition.
- The `try-except` block ensures that if the model isn't found (meaning it wasn't downloaded or is in an inaccessible location), the notebook provides a helpful message instead of crashing.

```ruby

import spacy
import re # For regular expressions, useful for text cleaning

# Load the small English language model
# This line might take a moment to run the first time you use the model in a session.
try:
    nlp = spacy.load("en_core_web_sm")
    print("spaCy model loaded successfully!")
except OSError:
    print("Error: Language model 'en_core_web_sm' not found.")
    print("Please ensure it was downloaded correctly by running the following in your terminal:")
    print("`python -m spacy download en_core_web_sm`")
    # If the model isn't found, we can't proceed, so we'll exit gracefully.
    raise SystemExit("SpaCy model not found. Please download it first.")

```


## Step 2: Define Text Cleaning Function

Before performing NER, it's a good practice to clean your raw text. However, for NER, "cleaning" should be conservative. We want to remove noise (like OCR errors or special tags) without removing information crucial for NER, such as capitalization (which helps identify proper nouns) or sentence structure.

This function will:
- Remove specific known artifacts like "[unclear]" tags.
- Normalize whitespace by replacing multiple spaces, tabs, and newlines with a single space.


### Explanation:

- The function takes `text_content` as input.
- It uses `replace()` to target specific strings that are noise.
- `re.sub(r'\s+', ' ', cleaned_text).strip()` is a regular expression that finds one or more whitespace characters (`\s+`) and replaces them with a single space.
- `.strip()` removes leading/trailing whitespace. This ensures consistent spacing.

**Important**: Avoid aggressive cleaning for NER, such as:
- Lowercasing (NER relies on capitalization)
- Removing all punctuation (NER relies on sentence boundaries)
- Removing stopwords (context is important for NER)

```ruby

def clean_text_for_ner(text_content):
    """
    Performs conservative text cleaning suitable for NER.
    Focuses on removing noise without losing crucial information like capitalization.
    """
    # Replace common OCR artifacts or specific unwanted tags (in the case of our document, tags like "[unclear]")
    cleaned_text = text_content.replace("[unclear]", " ")

    # Normalize whitespace: replace multiple spaces/newlines with a single space
    # This helps spaCy's tokenizer work more consistently
    cleaned_text = re.sub(r'\s+', ' ', cleaned_text).strip()

    return cleaned_text
```


### Step 4: Define NER Execution Function

The `perform_ner()` function below will take the cleaned text and the loaded spaCy model, then run the NER process. It will extract all identified PERSON (people's names) and GPE (geopolitical entity) / LOC (location) entities. These lists will be "raw" at this stage, meaning they might still contain some false positives before post-processing.

#### Explanation:

- `doc = nlp_model(text_to_process)`: This is where spaCy processes the text and identifies entities.
- `doc.ents`: This is a list of all identified entities in the doc object.
- The loop iterates through these entities, checking their `label_` (e.g., "PERSON", "GPE", "LOC") and appends the entity's text (`ent.text`) to the appropriate raw list.

```ruby
def perform_ner(text_to_process, nlp_model):
    """
    Runs spaCy's NER on the given text and extracts raw PERSON, GPE, and LOC entities.
    """
    doc = nlp_model(text_to_process)

    raw_people_names = []
    raw_place_names = []

    for ent in doc.ents:
        if ent.label_ == "PERSON":
            raw_people_names.append(ent.text)
        elif ent.label_ == "GPE" or ent.label_ == "LOC":
            raw_place_names.append(ent.text)

    return raw_people_names, raw_place_names

```


### Step 5: Run NER

Let's now put all the functions together: read the file, clean the text, perform NER.

```ruby

file_name = "AgreementWithTrucialChiefs.txt" # Ensure this file is in the same directory as your notebook
text_content = "" # Initialize to empty string

try:
    with open(file_name, 'r', encoding='utf-8') as file:
        text_content = file.read()
    print(f"Successfully read '{file_name}'. Length: {len(text_content)} characters.")
except FileNotFoundError:
    print(f"Error: The file '{file_name}' was not found. Please check the file name and path.")
except Exception as e:
    print(f"An error occurred while reading the file: {e}")

if text_content: # Only proceed if text_content was successfully loaded
    # 2. Perform Text Cleaning
    print("\n--- Performing Text Cleaning ---")
    cleaned_text = clean_text_for_ner(text_content)
    # Uncomment the line below to see a snippet of the cleaned text if you're curious:
    # print(f"Cleaned text snippet (first 500 chars): {cleaned_text[:500]}...")

    # 3. Perform NER on the cleaned text
    print("\n--- Performing Named Entity Recognition ---")
    raw_people, raw_places = perform_ner(cleaned_text, nlp)
    print(f"Raw PERSON entities found: {len(raw_people)}")
    print(f"Raw GPE/LOC entities found: {len(raw_places)}")

    # 4. Display Results
    print("\nIdentified People's Names (Raw):")
    if raw_people: # Check if the list is not empty after filtering
        for name in raw_people:
            print(f"- {name}")
    else:
        print("No people names identified.")

    print("\nIdentified Place Names (Raw):")
    if raw_places: # Check if the list is not empty after filtering
        for place in raw_places:
            print(f"- {place}")
    else:
        print("No place names identified.")
else:
    print("No content to process. Please ensure the file is correctly read.")

```

**Expected Output:**

Identified people names:

![Output](/assets/images/spacy/tutorial_spacy_5.1.png)


Identified place names:

![Output](/assets/images/spacy/tutorial_spacy_5.2.png)


### Step 6: Do Post-Processing for NER Output

You can see from the above result that spaCy's English Language model identified some entities incorrectly. NER models can sometimes make mistakes or pick up non-names (like titles, abbreviations, or common words), especially when working with historical documents. That's why it's important to do post-processing to refine the results. 

The `post_process_ner_output()` function below will filter the raw entities based on observed patterns of incorrect classifications. This step is highly customizable based on the specific noise you find in your document type.

#### Explanation:

- `exclude_patterns`: These lists are populated with the terms we've observed to be misclassified. They are defined separately for PERSON and PLACE types.
- `min_length`: Helps filter out very short, often meaningless, extractions.
- `ent.replace('\n', ' ').strip()` ensures consistent formatting of entities, even if they span multiple lines.

**Filtering Logic:**
  
- `any(excl_pat.lower() in normalized_ent.lower() for excl_pat in exclude_patterns)`: Checks if any of the unwanted patterns are contained within the extracted entity. This is robust for variations.
- `any(char.isdigit() for char in normalized_ent)`: Removes entries containing numbers (e.g., "No: 167", dates).
- Length check `len(normalized_ent) < min_length`: Filters out very short strings.
- Specific person-related cleanups (like handling "C.I.E." within a name or removing trailing punctuation) are included.
  
This function needs iterative refinement. As you review your output, if you see new types of incorrect entries, you can update the `exclude_patterns` lists.

```ruby

def post_process_ner_output(entities_list, entity_type):
    """
    Filters out common false positives from the NER output based on observed patterns.
    This list is flexible and should be refined iteratively after reviewing initial raw NER results.
    """
    filtered = []

    # Define exclusion patterns based on previous observations from your document
    # You can expand these lists as you find more "noise" in your output.
    if entity_type == "PERSON":
        exclude_patterns = [
            # Titles, honors, roles, and common abbreviations seen in raw output
            "C. E.", "C. I. E.", "C. J. E.", "L. S.", "L. S. True Copy", "Sheikh",
            "L. S. True Copy JHBaille Asst", "L. S. True", "JUBauille Asst", "Copy H. Baille Asst",
            "A.C. Talbot C. I. E. Political Resident",

            # Partial words or non-name phrases seen in raw output
            "Govern-", "Dated Umm-ul Kawain", "Dated Ajman", "Dated Sharjah", "Dated Abu Dhabi",

            # Time/Date related terms seen in raw output
            "Shaaban", "Hijri",

            # Filter this misspelling/variant
            "A.C. Tabot", "A. C. Talbot",

            # Specific places mistakenly identified as people 
            "Persian Gulf", "Umm-ul Kawain", "the Persian Gulf", "Rasal Khaimah", "Ras al Khaimah", "Umm al Kawain"
        ]
        min_length = 2 # Minimum length for a person's name to be considered valid

    
    elif entity_type == "PLACE":
        exclude_patterns = [
            "Return", "C.E.", "P.Gulf", "Foreign Dept", "Deptt", "Govt",  "Secry", "Ratification", "Fort",
            "Lieut", "M.Chapuy", "Talbot", "the Persian Gulf"
        ]
        min_length = 2 

    for ent in entities_list:
        # Normalize the entity text: replace newlines with spaces and strip extra whitespace
        normalized_ent = ent.replace('\n', ' ').strip()

        # Filter out if it matches any exclusion pattern (case-insensitive check)
        if any(excl_pat.lower() in normalized_ent.lower() for excl_pat in exclude_patterns):
            continue

        # Filter out if it contains digits (often indicates dates or IDs)
        if any(char.isdigit() for char in normalized_ent):
            continue

        # Filter out very short entities based on defined minimum length
        if len(normalized_ent) < min_length:
            continue
        
        # Additional specific filters or cleaning for PERSON entities
        if entity_type == "PERSON":
            # Remove trailing non-alphanumeric/non-space characters
            normalized_ent = re.sub(r'[\s.,-]+$', '', normalized_ent)
            # Remove leading numbers/symbols that might be OCR errors
            normalized_ent = re.sub(r'^[^\w\s]+', '', normalized_ent).strip()


        # If all filters pass, add to the filtered list
        filtered.append(normalized_ent)

    return filtered

```

```ruby

# Post-process the NER output
print("\n--- Post-processing NER Output ---")
# Apply filtering, then convert to a set to remove duplicates, then sort
final_people_names = sorted(list(set(post_process_ner_output(raw_people, "PERSON"))))
final_place_names = sorted(list(set(post_process_ner_output(raw_places, "PLACE"))))

# Display Results
print("\nIdentified People's Names (Filtered):")
if final_people_names: # Check if the list is not empty after filtering
    for name in final_people_names:
        print(f"- {name}")
else:
    print("No people names identified after filtering.")

print("\nIdentified Place Names (Filtered):")
if final_place_names: # Check if the list is not empty after filtering
    for place in final_place_names:
        print(f"- {place}")
else:
    print("No place names identified after filtering.")

```

**Expected Output:**

![Output](/assets/images/spacy/tutorial_spacy_6.1.png)

### Step 7: Saved Output as TXT

Below is the code to save the identified people and place names as TXT files for further analysis.

```ruby

# Save identified people's names to a text file
output_file_people = "people_names.txt"
with open(output_file_people, 'w', encoding='utf-8') as f:
    for name in final_people_names:
        f.write(name + '\n')
print(f"Identified people's names saved to '{output_file_people}'")

# Save identified place names to a text file
output_file_places = "place_names.txt"
with open(output_file_places, 'w', encoding='utf-8') as f:
    for place in final_place_names:
        f.write(place + '\n')
print(f"Identified place names saved to '{output_file_places}'")

```

**Expected Output:**

![Output](/assets/images/spacy/tutorial_spacy_7.1.png)

### Next Steps

Steps you can take for further improvement:

- **Review Remaining Errors**: Carefully examine any remaining incorrect entries in the "Cleaned and Filtered" lists. Add new patterns to the exclude_patterns in post_process_ner_output for these specific cases.
- **Consider Larger SpaCy Models**: If accuracy is still a concern, en_core_web_lg (large) or en_core_web_trf (transformer-based) models from spaCy offer higher accuracy but require more memory and computational resources. You would install them using python -m spacy download en_core_web_lg.
- **Custom Rule-Based Matching**: For very specific patterns that spaCy's statistical model misses (e.g., consistently formatted IDs or very specific types of titles), you could use spaCy's Matcher or EntityRuler components.
- **Custom Model Training**: For truly high accuracy on highly specialized texts, the best approach is to create a custom dataset by manually annotating a portion of your documents and then training a spaCy NER model on it. This is a more advanced task.