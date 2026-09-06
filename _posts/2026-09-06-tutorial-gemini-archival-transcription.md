---
title: "Transcribing Archival Images with Multimodal AI: A Gemini API Workflow"
categories:
  - Intermediate
tags:
  - Generative AI
  - Gemini API
  - Transcription
  - Archives
toc: true
toc_sticky: true
toc_label: "Table of Contents"
---

This tutorial introduces a reproducible workflow for transcribing archival document images with **multimodal generative AI**. We use the **Gemini API Free Tier** as the worked example, but the larger method is not specific to Gemini. The same basic workflow can be adapted to other multimodal AI systems that accept images as input.

You will learn how to send an archival image to an AI model, give the model explicit transcription instructions, retrieve and save the transcription, and evaluate the output against the original image.

The central historical-method question is:

> **What happens when AI-generated text becomes part of the evidentiary record we search, analyze, or reuse?**

**Learning Objectives:**

By the end of this tutorial, you will be able to:

* Explain the difference between using an AI chat interface and using an API.
* Send an archival image and transcription prompt to a multimodal AI model.
* Explain what the main lines of the API code are doing.
* Compare a minimal transcription prompt with a more explicit archival transcription prompt.
* Save model outputs together with information about how they were produced.
* Apply the same transcription workflow to several archival images.
* Evaluate AI-generated transcriptions for omissions, normalization, uncertainty, layout problems, and unsupported reconstruction.
* Explain which parts of the workflow can be adapted to other multimodal AI systems.

**Prerequisites:**

This tutorial assumes basic familiarity with Jupyter Notebook and simple Python code. If you are new to notebooks, complete the *Getting Started with Jupyter Notebook* tutorial first.

You do **not** need previous experience with APIs.

> **Last reviewed:** September 2026

<div class="notice--info" markdown="1">

**Prefer a ready-to-run notebook?**

Download the companion notebook:

[**Download `GeminiArchivalTranscription.ipynb`**](/assets/notebooks/GeminiArchivalTranscription.ipynb)

You can run the notebook directly or use it alongside the step-by-step explanations below.

</div>

---

## 1. What Are We Doing in This Tutorial?

We will begin with an archival image stored on your computer.

For example:

```text
page_01.jpg
```

We will then use Python in a Jupyter Notebook to send two things to Gemini:

1. the archival image;
2. written instructions telling the model how we want the image transcribed.

Gemini will return a text response containing its transcription.

The basic workflow looks like this:

```text
archival image on your computer
          ↓
Python code in Jupyter
          ↓
image + transcription instructions
          ↓
Gemini API
          ↓
multimodal Gemini model
          ↓
text transcription returned to Jupyter
          ↓
human review against the image
```

Later, we will repeat the process across several images and save the results in a CSV file.

---

## 2. Multimodal AI Is Not the Same as Traditional OCR

Traditional OCR and HTR systems are designed specifically to recognize text from images.

Multimodal generative AI models are broader systems. They can interpret images and language together and can therefore respond to instructions about text, document structure, handwriting, layout, stamps, marginalia, tables, and other visible features.

That flexibility can be useful for complex archival material. It also creates a methodological risk.

A multimodal generative model may produce text that looks convincing while silently:

* normalizing spelling,
* expanding abbreviations,
* changing punctuation,
* omitting marginal notes,
* reorganizing reading order,
* guessing unclear words,
* or reconstructing text that is not securely visible.

For historical research:

> **A plausible transcription is not necessarily a faithful transcription.**

We will therefore treat the model output as a **transcription candidate** that must be evaluated against the source image.

---

## 3. Why Use an API Instead of a Chat Interface?

You could upload an image to a consumer AI chat interface and ask it to transcribe the page.

An **API** lets us perform the same basic interaction through code. This becomes useful when we want to:

* apply exactly the same prompt to several pages;
* keep a record of which prompt was used;
* record which model produced each transcription;
* save the outputs automatically;
* compare different prompts;
* repeat the workflow later;
* eventually process more than a few pages.

An API does **not** automatically make the model more accurate. Its main advantage here is that it makes the process more **repeatable and documentable**.

### 3.1 What Is an API?

API stands for **Application Programming Interface**.

For this tutorial, you can think of an API as a structured way for your Python code to communicate with an online AI service.

Your notebook sends a request:

```text
Here is an image.
Here are my instructions.
Please return a transcription.
```

The service processes that request and sends a response back to your notebook.

The archival image therefore leaves your computer and is processed by the external service.

---

## 4. Why Gemini?

This tutorial uses the **Gemini API** because its Free Tier provides an accessible way for students to experiment with multimodal models that accept images as input.

The larger workflow is **not specific to Gemini**. Other multimodal AI systems can perform similar image-to-text tasks, although their Python libraries, authentication methods, model names, usage limits, pricing, and data-handling policies will differ.

The transferable workflow is:

```text
authenticate
→ provide image
→ provide transcription instructions
→ receive output
→ save information about the process
→ evaluate against the source
```

If you later switch to another provider, the API-specific Python code will change. The historical-method questions about **fidelity, uncertainty, provenance, and evaluation** remain.

### 4.1 Free Tier and Data Use

At the time this tutorial was last reviewed, Google offered free input and output for selected Gemini API models, including **Gemini 3.7 Flash**. Free-tier availability, quotas, and terms can change, so check the current [Gemini API pricing](https://ai.google.dev/gemini-api/docs/pricing){:target="_blank" rel="noopener"} and [rate limits](https://ai.google.dev/gemini-api/docs/rate-limits){:target="_blank" rel="noopener"} before using the service.

Google currently states that content submitted through the Free Tier may be used to improve its products, while paid-tier content is not used for that purpose.

> **Privacy and source restrictions:** Use only public or unrestricted historical images for this tutorial. Do not submit confidential, restricted, personally sensitive, or otherwise non-shareable material to an external AI service. Before processing research materials through any API, check both the provider's current data-use terms and the requirements of the archive, collection, institution, or research agreement governing your sources.

---

## 5. Prepare Your Project Folder

Create a folder for the tutorial. Inside it, create another folder called:

```text
archival_images
```

Your project should look something like this:

```text
gemini-transcription/
├── GeminiArchivalTranscription.ipynb
└── archival_images/
    └── page_01.jpg
```

For the first part of the tutorial, we will work with **one JPG image** called:

```text
page_01.jpg
```

Using one file type at first keeps the Python code easier to understand.

Place a public or unrestricted archival image inside the ```archival_images``` folder and rename it ```page_01.jpg```.

> **Why does the folder structure matter?** Python needs to know where a file is located before it can open it. Because our notebook and ```archival_images``` folder are stored together, we can refer to the image with the simple relative path ```archival_images/page_01.jpg```.

---

## 6. Install the Python Libraries

We need three main packages:

* **google-genai** — Google's current Python SDK for the Gemini API;
* **pandas** — helps us organize and save transcription results in tables;
* Python's built-in file-handling tools — used to read the image and prepare it for the request.

Run this in a Jupyter code cell:

```python
%pip install -U google-genai pandas
```

### What does this line do?

```text
%pip install
```

asks the Python environment used by your notebook to install packages.

```text
-U
```

means **upgrade** the packages if an older version is already installed.

Now import the tools we will use:

```python
from google import genai
from getpass import getpass
from pathlib import Path
from datetime import datetime, timezone
import base64
import time
import pandas as pd
```

### What are these imports for?

| Import | Why we need it |
| --- | --- |
| ```genai``` | Communicates with the Gemini API |
| ```getpass``` | Lets us enter an API key without displaying it in the notebook |
| ```Path``` | Helps Python work with file and folder paths |
| ```datetime, timezone``` | Records when a transcription was produced |
| ```base64``` | Converts image bytes into text-safe data that can be sent in the API request |
| ```time``` | Lets us pause briefly between API requests |
| ```pandas``` | Organizes and saves results in tables |

You do not need to memorize these imports. The important thing is to understand that each one adds a specific capability that we use later.

---

## 7. Create and Enter Your Gemini API Key

The API needs to know which project is making the request. An **API key** provides that authentication.

### 7.1 Create the Key

1. Open [Google AI Studio](https://aistudio.google.com/){:target="_blank" rel="noopener"}.
2. Go to the API Keys section.
3. Create or copy an API key for your project.
4. Return to your notebook.

### 7.2 Enter the Key Securely

Run:

```python
GEMINI_API_KEY = getpass("Enter your Gemini API key: ")
```

When you run the cell, Jupyter asks you to enter the key. Because we use ```getpass```, the key is **not displayed in the notebook output**.

Now create a Gemini client:

```python
client = genai.Client(api_key=GEMINI_API_KEY)
```

### What is a client?

The ```client``` is the Python object we will use to communicate with Gemini.

You can think of it as the connection between your notebook and the Gemini API.

> **Never write an API key directly into a notebook that you plan to save, publish, or upload to GitHub.** For longer-term projects, environment variables are a better way to store credentials. The interactive method above is used here because it is simple and prevents the key from being stored visibly in the teaching notebook.

---

## 8. Choose the Model

We will store the model name in a variable:

```python
MODEL = "gemini-3.7-flash"
```

### Why put the model name in a variable?

We could write the model name inside every API request. Instead, we define it once and later write:

```python
model=MODEL
```

If Google changes its available models, we only need to update **one line**.

At the time this tutorial was reviewed, Gemini 3.7 Flash supports image input and is available on the Gemini API Free Tier. If it is no longer available in your account, choose a current image-capable model in Google AI Studio and replace the value assigned to ```MODEL```.

---

## 9. Tell Python Where the Image Is

Create a path pointing to the archival image:

```python
IMAGE_PATH = Path("archival_images/page_01.jpg")
```

This means:

```text
start from the folder containing the notebook
→ open archival_images
→ find page_01.jpg
```

Now check that Python can actually find the file:

```python
if not IMAGE_PATH.exists():
    raise FileNotFoundError(f"Could not find {IMAGE_PATH}")
```

### What does this check do?

```python
IMAGE_PATH.exists()
```

asks whether the file exists at the location we gave Python.

If the cell runs without producing an error, Python found the image successfully.

---

## 10. Create a Function That Sends an Image to Gemini

We could write the full API request every time we want a transcription. Instead, we will create a **function** called ```transcribe_image```.

A function is a reusable block of code. We give it:

1. an image path;
2. a prompt;
3. a model.

It reads the image, prepares it for the API request, sends the image and prompt to Gemini, and returns the model's text response.

Run:

```python
def transcribe_image(image_path, prompt, model=MODEL):
    image_path = Path(image_path)

    with open(image_path, "rb") as f:
        image_bytes = f.read()

    image_b64 = base64.b64encode(image_bytes).decode("utf-8")

    interaction = client.interactions.create(
        model=model,
        input=[
            {"type": "text", "text": prompt},
            {
                "type": "image",
                "data": image_b64,
                "mime_type": "image/jpeg"
            }
        ]
    )

    return interaction.output_text
```

### What is each part doing?

The first line defines the function:

```python
def transcribe_image(image_path, prompt, model=MODEL):
```

The function expects an image path, a prompt, and a model.

This block opens the image as **binary data**:

```python
with open(image_path, "rb") as f:
    image_bytes = f.read()
```

```text
rb
```

means **read binary**. Images are stored as bytes rather than ordinary text.

The next line converts those bytes into Base64:

```python
image_b64 = base64.b64encode(image_bytes).decode("utf-8")
```

Base64 is simply a way to represent binary data in a form that can be included safely inside the API request. You do not need to memorize the conversion code.

This is the actual API request:

```python
interaction = client.interactions.create(...)
```

Inside ```input``` we send two items:

```text
our written prompt
+
our image
```

The line:

```python
"mime_type": "image/jpeg"
```

tells Gemini what kind of image we are sending.

Finally:

```python
return interaction.output_text
```

returns the transcription text that Gemini sent back.

Once this function exists, we can transcribe an image with the much simpler instruction:

```python
transcribe_image(IMAGE_PATH, SOME_PROMPT)
```

---

## 11. First Attempt: Use a Minimal Prompt

We will deliberately begin with a short prompt:

```python
MINIMAL_PROMPT = """
Transcribe all text visible in this archival document image.
Return only the transcription.
"""
```

We are saving the transcription instructions inside a variable called ```MINIMAL_PROMPT```. This lets us reuse exactly the same wording later.

Now send the image and prompt to Gemini:

```python
minimal_transcription = transcribe_image(
    IMAGE_PATH,
    MINIMAL_PROMPT
)
```

This means:

> Run our ```transcribe_image``` function using ```page_01.jpg``` and the minimal prompt, then save Gemini's response in a variable called ```minimal_transcription```.

Display the result:

```python
print(minimal_transcription)
```

Now compare the output directly with the archival image.

Ask:

* Did the model include all visible text?
* Did it preserve names and dates?
* Did it change spelling?
* Did it expand abbreviations?
* Did it omit marginalia, stamps, or annotations?
* Did it silently guess unclear words?
* Did it change the reading order?

The minimal prompt leaves many transcription decisions unspecified. The model therefore has to decide for itself how to handle ambiguity, layout, abbreviations, and unclear text.

---

## 12. Second Attempt: Give More Explicit Archival Instructions

Now we will provide more detailed transcription rules.

```python
ARCHIVAL_PROMPT = """
Transcribe the visible text in this archival document image as faithfully as possible.

Follow these rules:

1. Preserve visible spelling, capitalization, punctuation, and abbreviations where legible.
2. Do not modernize spelling, names, or place names.
3. Do not silently expand abbreviations.
4. Preserve line breaks and reading order where they are meaningful.
5. Include marginalia, stamps, seals, headings, and handwritten or typed annotations when visible.
6. If text is illegible, write [illegible].
7. If you can suggest a reading but are uncertain, write [uncertain: your reading].
8. Do not reconstruct missing or obscured text from context.
9. Do not add explanations, summaries, or historical commentary.
10. Return only the transcription.
"""
```

### Why make the prompt longer?

The model still has to interpret the image, but we are now making our **transcription policy** explicit.

For example, ```Do not modernize spelling``` tells the model that historical spelling should be preserved, while ```If text is illegible, write [illegible]``` gives it a way to represent uncertainty rather than inventing a confident reading.

Now run the same image again with the new prompt:

```python
archival_transcription = transcribe_image(
    IMAGE_PATH,
    ARCHIVAL_PROMPT
)
```

Display it:

```python
print(archival_transcription)
```

The longer prompt does **not** guarantee that the transcription will be correct. Instead, it lets us investigate whether more explicit archival instructions change the model's behavior.

---

## 13. Compare the Two Transcriptions

We now have two outputs for the same image:

```text
minimal_transcription
```

and:

```text
archival_transcription
```

To make them easier to compare, put them into a small table:

```python
comparison_df = pd.DataFrame([
    {
        "prompt_type": "minimal",
        "transcription": minimal_transcription
    },
    {
        "prompt_type": "archival",
        "transcription": archival_transcription
    }
])

comparison_df
```

### What is this code doing?

```python
pd.DataFrame(...)
```

creates a table.

Each pair of curly brackets becomes one row. The resulting table has two columns:

| prompt_type | transcription |
| --- | --- |
| minimal | Gemini's first output |
| archival | Gemini's second output |

Do **not** decide which prompt is better simply by asking which transcription looks more polished. Compare both outputs against the source image.

---

## 14. Evaluate the Transcriptions Systematically

Looking at the two outputs is useful, but we can make the comparison more systematic.

For this exercise, use a simple **2 / 1 / 0 rubric**:

* **2 = strong:** largely faithful in this category;
* **1 = mixed:** partly successful, but with noticeable problems;
* **0 = weak:** substantial problems.

We will evaluate:

| Category | What to Check |
| --- | --- |
| General accuracy | Is the visible text represented faithfully overall? |
| Named entities | Are people, places, offices, and organizations accurate? |
| Dates and references | Are dates, numbers, document references, and identifiers preserved? |
| Abbreviations | Are abbreviations preserved rather than silently expanded? |
| Layout / reading order | Does the transcription follow the document's meaningful reading order? |
| Marginalia / stamps / annotations | Are non-body-text elements represented? |
| Uncertainty marking | Does the model identify unclear readings rather than hiding uncertainty? |
| Omissions | Is visible text missing? |
| Unsupported reconstruction | Has the model supplied text that cannot be justified from the image? |

Create an empty evaluation table:

```python
evaluation_categories = [
    "general_accuracy",
    "named_entities",
    "dates_references",
    "abbreviations",
    "layout_reading_order",
    "marginalia_stamps_annotations",
    "uncertainty_marking",
    "omissions",
    "unsupported_reconstruction"
]

evaluation_df = pd.DataFrame({
    "category": evaluation_categories,
    "minimal_score": "",
    "archival_score": "",
    "notes": ""
})

evaluation_df
```

The score columns are deliberately empty. **You**, not the model, fill them in after comparing each transcription against the source image.

The ```notes``` column is particularly important. A number alone cannot explain what went wrong.

> **The rubric organizes human judgment; it does not replace human judgment.**

---

## 15. Save the Transcriptions and Record How They Were Produced

If an AI-generated transcription becomes part of a research workflow, saving only the transcription text is not enough.

We should also record:

* which source image was used;
* which prompt was used;
* which model was used;
* when the request was made.

First record the current time:

```python
processed_at = datetime.now(timezone.utc).isoformat()
```

Now create a table containing both transcriptions:

```python
results_df = pd.DataFrame([
    {
        "image_file": IMAGE_PATH.name,
        "prompt_type": "minimal",
        "prompt": MINIMAL_PROMPT.strip(),
        "model": MODEL,
        "processed_at_utc": processed_at,
        "transcription": minimal_transcription
    },
    {
        "image_file": IMAGE_PATH.name,
        "prompt_type": "archival",
        "prompt": ARCHIVAL_PROMPT.strip(),
        "model": MODEL,
        "processed_at_utc": processed_at,
        "transcription": archival_transcription
    }
])

results_df
```

Each row now tells us:

```text
which image
+
which prompt
+
which model
+
when it was processed
+
what transcription was returned
```

Save the table as a CSV:

```python
results_df.to_csv(
    "transcription_comparison.csv",
    index=False,
    encoding="utf-8-sig"
)

print("Saved transcription_comparison.csv")
```

```to_csv``` means **save this pandas table as a CSV file**.

```index=False``` prevents pandas from adding an unnecessary numbered index column.

```encoding="utf-8-sig"``` helps preserve multilingual characters when the CSV is opened in commonly used spreadsheet software.

---

## 16. Process Several Images

So far, we have transcribed one page at a time.

One advantage of using an API is that Python can repeat the same workflow across several files automatically.

For this teaching exercise, we will keep the example simple and use **JPG images only**.

Place several images in your folder:

```text
archival_images/
├── page_01.jpg
├── page_02.jpg
├── page_03.jpg
└── page_04.jpg
```

### 16.1 Ask Python to Find the Images

Run:

```python
IMAGE_FOLDER = Path("archival_images")

image_files = sorted(
    IMAGE_FOLDER.glob("*.jpg")
)

print(f"Found {len(image_files)} image(s).")
```

### What is this code doing?

```python
IMAGE_FOLDER = Path("archival_images")
```

tells Python which folder to look inside.

```python
IMAGE_FOLDER.glob("*.jpg")
```

means:

> Find every file in that folder whose filename ends in ```.jpg```.

The asterisk ```*``` is a wildcard meaning **any filename**.

```python
sorted(...)
```

puts the files in alphabetical order.

Finally:

```python
len(image_files)
```

counts how many images Python found.

You can inspect the list with:

```python
image_files
```

### 16.2 Create an Empty Place to Store the Results

Before processing the images, create an empty list:

```python
batch_results = []
```

At this point the list contains nothing. As Python processes each image, we will add one record containing the filename, prompt, model, timestamp, transcription, and fields for later human review.

### 16.3 Loop Through the Images One at a Time

Now run:

```python
for image_path in image_files:
    print(f"Transcribing: {image_path.name}")

    try:
        transcription = transcribe_image(
            image_path,
            ARCHIVAL_PROMPT
        )

        batch_results.append({
            "image_file": image_path.name,
            "prompt_type": "archival",
            "prompt": ARCHIVAL_PROMPT.strip(),
            "model": MODEL,
            "processed_at_utc": datetime.now(
                timezone.utc
            ).isoformat(),
            "transcription": transcription,
            "review_status": "",
            "review_notes": ""
        })

    except Exception as e:
        batch_results.append({
            "image_file": image_path.name,
            "prompt_type": "archival",
            "prompt": ARCHIVAL_PROMPT.strip(),
            "model": MODEL,
            "processed_at_utc": datetime.now(
                timezone.utc
            ).isoformat(),
            "transcription": "",
            "review_status": "API error",
            "review_notes": str(e)
        })

    time.sleep(2)
```

This looks longer than our earlier code, so read it as a sequence of steps rather than as one large block.

#### Step A: Repeat the code for every image

```python
for image_path in image_files:
```

means:

> Take the first image in ```image_files```, run the indented code below it, then move to the second image, then the third, and continue until every image has been processed.

This is called a **loop**.

#### Step B: Tell us which file is being processed

```python
print(f"Transcribing: {image_path.name}")
```

prints something like:

```text
Transcribing: page_02.jpg
```

#### Step C: Send the image to Gemini

```python
transcription = transcribe_image(
    image_path,
    ARCHIVAL_PROMPT
)
```

uses the same function and same archival prompt that we tested earlier. The only thing changing each time is ```image_path```.

#### Step D: Add the successful result to our list

```python
batch_results.append({...})
```

means **add one new record to ```batch_results```**.

We also create two empty fields:

```text
review_status
review_notes
```

so a human reviewer can later record whether the transcription has been checked.

#### Step E: What do ```try``` and ```except``` do?

An API request can fail because of an internet problem, rate limit, invalid image, or temporary service error.

```python
try:
```

means **try to process this image normally**.

```python
except Exception as e:
```

means **if that fails, record the error instead of stopping the entire batch**.

That way, one problematic page does not prevent the remaining pages from being processed.

#### Step F: Pause briefly

```python
time.sleep(2)
```

asks Python to wait two seconds before sending the next request.

This is a simple precaution for a small teaching exercise. It is **not** a universal rate-limit rule. For larger projects, check the current limits for your model and account.

### 16.4 Turn the Results into a Table

After the loop finishes:

```python
batch_df = pd.DataFrame(batch_results)

batch_df
```

This converts our list of results into a pandas table. Each image becomes one row.

### 16.5 Save the Batch Transcriptions

Run:

```python
batch_df.to_csv(
    "archival_transcriptions.csv",
    index=False,
    encoding="utf-8-sig"
)

print("Saved archival_transcriptions.csv")
```

You now have a CSV containing the automatically generated transcriptions and the information needed to trace how each one was produced.

---

## 17. What Changes When We Scale Up?

Processing 500 pages is not simply the same activity as transcribing one page more quickly.

Automation can make model behavior **systematic** across an entire corpus.

Suppose a model routinely drops marginalia, normalizes historical spelling, mishandles particular names, changes reading order, or reconstructs uncertain text confidently. If we process hundreds of pages automatically, those tendencies can become properties of the dataset itself.

This matters because transcriptions may later be used for keyword search, named-entity recognition, topic modeling, embeddings, retrieval-augmented generation, quantitative text analysis, or structured dataset creation.

Before scaling an AI transcription workflow:

1. test it on a representative sample;
2. define what counts as an acceptable transcription;
3. inspect recurring failure patterns;
4. decide what level of human review is required.

---

## 18. Adapting the Workflow to Other Multimodal Models

The Python code in this tutorial is specific to Gemini. The **research workflow is not**.

With another multimodal AI system, you would still need to:

1. authenticate with the service;
2. select a model that accepts images;
3. provide the archival image;
4. provide explicit transcription instructions;
5. receive the model output;
6. save information about how the output was produced;
7. evaluate the transcription against the source.

The exact SDK functions will differ.

> **The provider-specific code is replaceable; the decisions about transcription fidelity, provenance, uncertainty, and evaluation are not.**

---

## 19. What Could You Do Next?

You can extend this workflow by:

* comparing two or more multimodal models on the same archival pages;
* testing different transcription prompts;
* creating a manually checked reference transcription;
* calculating character or word error rates where appropriate;
* comparing generative AI with specialized OCR/HTR systems;
* extracting structured information from reviewed transcriptions;
* linking place names to a gazetteer;
* building search or retrieval workflows over a transcribed corpus.

A natural next tutorial is **From AI Transcription to Structured Historical Data**.

---

## 20. Further Resources

### Gemini API

* [Gemini API documentation](https://ai.google.dev/gemini-api/docs){:target="_blank" rel="noopener"}
* [Getting started with the Gemini API](https://ai.google.dev/gemini-api/docs/get-started){:target="_blank" rel="noopener"}
* [Image understanding](https://ai.google.dev/gemini-api/docs/image-understanding){:target="_blank" rel="noopener"}
* [Using Gemini API keys](https://ai.google.dev/gemini-api/docs/api-key){:target="_blank" rel="noopener"}
* [Gemini API pricing](https://ai.google.dev/gemini-api/docs/pricing){:target="_blank" rel="noopener"}
* [Gemini API rate limits](https://ai.google.dev/gemini-api/docs/rate-limits){:target="_blank" rel="noopener"}

### Related Toolkit Tutorials

* *Getting Started with Jupyter Notebook*
* *Trucial Coast Towns: Building a Historical Gazetteer Dataset with GeoNames*
