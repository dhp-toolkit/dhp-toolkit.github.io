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

This tutorial introduces a reproducible workflow for transcribing archival document images with **multimodal generative AI**. We use the **Gemini API Free Tier** as a worked example, but the larger method is not specific to Gemini. The same basic workflow can be adapted to other multimodal AI systems that accept images as input.

You will learn how to send an archival image to an AI model, give the model explicit transcription instructions, retrieve and save the transcription, and evaluate the output against the original image.

The central historical-method question is:

> **What happens when AI-generated text becomes part of the evidentiary record we search, analyze, or reuse?**

**Learning Objectives**

By the end of this tutorial, you will be able to:

* Explain the difference between using an AI chat interface and using an API.
* Send an archival image and transcription prompt to a multimodal AI model.
* Explain what the main pieces of the API code are doing.
* Compare a minimal transcription prompt with a more explicit archival transcription prompt.
* Save model outputs together with information about how they were produced.
* Apply the same transcription workflow to several archival images.
* Evaluate AI-generated transcriptions for omissions, normalization, uncertainty, layout problems, and unsupported reconstruction.
* Explain which parts of the workflow can be adapted to other multimodal AI systems.

**Prerequisites**

This tutorial assumes basic familiarity with Jupyter Notebook and simple Python code. If you are new to notebooks, complete the *Getting Started with Jupyter Notebook* tutorial first.

You do **not** need previous experience with APIs.

> **Last reviewed:** September 2026

<div class="notice--info" markdown="1">

**Use the companion Jupyter Notebook**

You can use the notebook in three ways:

**[Open in Google Colab](https://colab.research.google.com/github/dhp-toolkit/dhp-toolkit.github.io/blob/master/assets/notebooks/GeminiArchivalTranscription.ipynb)**  
Runs the notebook interactively in your browser. No local Jupyter installation is required.

**[View the notebook on GitHub](https://github.com/dhp-toolkit/dhp-toolkit.github.io/blob/master/assets/notebooks/GeminiArchivalTranscription.ipynb)**  
Lets you read the notebook and inspect its code in your browser. The cells cannot be run from the GitHub preview.

<a href="/assets/notebooks/GeminiArchivalTranscription.ipynb" download="GeminiArchivalTranscription.ipynb"><strong>Download the notebook</strong></a>  
Download the `.ipynb` file if you want to open it locally in Jupyter Notebook or JupyterLab.

</div>

---

## 1. What Are We Doing in This Tutorial?

We will begin with an archival image stored on your computer or uploaded to Google Colab.

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
archival image
      ↓
Python code in Jupyter or Colab
      ↓
image + transcription instructions
      ↓
Gemini API
      ↓
multimodal Gemini model
      ↓
text transcription returned to the notebook
      ↓
human review against the image
```

Later, we will repeat the process across several images and save the results in a CSV file.

---

## 2. Multimodal AI Is Not the Same as Traditional OCR

Traditional OCR and HTR systems are designed specifically to recognize text from images.

Multimodal generative AI models are broader systems. They can interpret images and language together and can therefore respond to instructions about:

* text,
* document structure,
* handwriting,
* layout,
* stamps,
* marginalia,
* tables,
* and other visible features.

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

You could upload an image to a consumer AI chat interface and ask:

```text
Please transcribe this page.
```

An **API** lets us perform the same basic interaction through code.

This becomes useful when we want to:

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

This means that the archival image **leaves your computer or Colab environment and is transmitted to the external AI service for processing**.

That matters when deciding which research materials are appropriate to use.

---

## 4. Why Gemini?

This tutorial uses the **Gemini API** because its Free Tier provides an accessible way for students to experiment with multimodal models that accept images as input.

The larger workflow is **not specific to Gemini**.

Other multimodal AI systems can perform similar image-to-text tasks, although their:

* Python libraries,
* authentication methods,
* model names,
* usage limits,
* pricing,
* and data-handling policies

will differ.

The transferable workflow is:

```text
authenticate
→ provide image
→ provide transcription instructions
→ receive output
→ record how the output was produced
→ evaluate against the source
```

If you later switch to another provider, the API-specific Python code will change. The historical-method questions about **fidelity, uncertainty, provenance, and evaluation** remain.

### 4.1 Free Tier and Data Use

At the time this tutorial was last reviewed, Google offered a Gemini API Free Tier with free input and output for selected models, including **Gemini 3.8 Flash**.

Free-tier model availability, quotas, pricing, and terms can change, so always check the current:

* [Gemini API pricing](https://ai.google.dev/gemini-api/docs/pricing)
* [Gemini API rate limits](https://ai.google.dev/gemini-api/docs/rate-limits)

before beginning a larger project.

Google currently states that content submitted through the Free Tier may be used to improve its products, while paid-tier content is not used for that purpose.

> **Privacy and source restrictions:** Use only public or unrestricted historical images for this tutorial. Do not submit confidential, restricted, personally sensitive, or otherwise non-shareable material to an external AI service. Before processing research materials through any API, check both the provider's current data-use terms and the requirements of the archive, collection, institution, or research agreement governing your sources.

---

## 5. Prepare Your Project

There are two ways to follow this tutorial.

### 5.1 If You Are Using Jupyter Notebook or JupyterLab Locally

Create a folder for the tutorial.

Inside it, create another folder called:

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

For the first part of the tutorial, we will work with one JPG image called:

```text
page_01.jpg
```

Place a public or unrestricted archival image inside the `archival_images` folder and rename it `page_01.jpg`.

### 5.2 If You Are Using Google Colab

You do not need to create the same folder structure on your computer.

Open the notebook in Colab, then:

1. Click the **Files** folder icon on the left side of the Colab window.
2. Click the **Upload** button.
3. Select your archival image.
4. Upload a JPG image called `page_01.jpg`.

Colab places uploaded files inside its temporary `/content` directory.

> **Important:** Files uploaded directly to a Colab session are temporary. If the session ends or resets, you may need to upload them again.

---

## 6. Install the Python Libraries

We need several pieces of software:

* **google-genai** — Google's current Python library for communicating with the Gemini API;
* **Pillow** — lets Python open and display image files;
* **pandas** — helps us organize and save transcription results in tables.

Run this in a Jupyter or Colab code cell:

```python
%pip install -U google-genai pillow pandas
```

### What Does This Line Do?

```text
%pip install
```

asks the Python environment used by your notebook to install packages.

```text
-U
```

means **upgrade** the packages if an older version is already installed.

The remaining words are the names of the packages we want.

Now import the tools we will use:

```python
from google import genai
from getpass import getpass
from pathlib import Path
from datetime import datetime, timezone
from PIL import Image
from IPython.display import display
import base64
import mimetypes
import time
import pandas as pd
```

### What Are These Imports For?

| Import | Why we need it |
| --- | --- |
| `genai` | Communicates with the Gemini API |
| `getpass` | Lets us enter an API key without displaying it |
| `Path` | Helps Python work with file and folder paths |
| `datetime, timezone` | Records when a transcription was produced |
| `Image` | Opens archival images |
| `display` | Displays an image inside Jupyter or Colab |
| `base64` | Converts the image into a form that can be transmitted through the API |
| `mimetypes` | Helps Python identify the image format |
| `time` | Lets us pause briefly between API requests |
| `pandas` | Organizes and saves results in tables |

You do not need to memorize these imports. The important point is that each one adds a specific capability that we use later.

---

## 7. Create and Enter Your Gemini API Key

The API needs to know which Google project is making the request. An **API key** provides that authentication.

### 7.1 Create the Key

1. Open [Google AI Studio](https://aistudio.google.com/).
2. Go to the API Keys section.
3. Create or copy an API key for your project.
4. Return to your notebook.

### 7.2 Enter the Key Securely

Run:

```python
GEMINI_API_KEY = getpass("Enter your Gemini API key: ")
```

When you run the cell, Jupyter or Colab will ask you to enter the key.

Because we use `getpass`, the key is **not displayed in the notebook output**.

Now create a Gemini client:

```python
client = genai.Client(api_key=GEMINI_API_KEY)
```

### What Is a Client?

The `client` is the Python object we will use to communicate with Gemini.

You can think of it as the connection between:

```text
your notebook
```

and:

```text
the Gemini API
```

Later, when we write:

```python
client.interactions.create(...)
```

we are telling this client to send a request to a Gemini model.

> **Never write an API key directly into a notebook that you plan to save, publish, or upload to GitHub.** For longer-term projects, environment variables or other credential-management tools are preferable. The interactive method above is used here because it is simple and prevents the key from being stored visibly in the teaching notebook.

---

## 8. Choose the Model

We will store the model name in a variable:

```python
MODEL = "gemini-3.8-flash"
```

### Why Put the Model Name in a Variable?

We could write `"gemini-3.8-flash"` inside every API request.

Instead, we define it once:

```python
MODEL = "gemini-3.8-flash"
```

and later write:

```python
model=MODEL
```

If Google changes its available models, we then only need to update **one line**.

At the time this tutorial was reviewed, Gemini 3.8 Flash accepts image input and is available through the Gemini API Free Tier.

If this model is no longer available in your account, choose a current image-capable Gemini model in Google AI Studio and replace the value assigned to `MODEL`.

---

## 9. Tell Python Where the Image Is

The path you use depends on whether you are working locally or in Google Colab.

### 9.1 Local Jupyter Notebook or JupyterLab

If your notebook and `archival_images` folder are arranged as shown earlier, use:

```python
IMAGE_PATH = Path("archival_images/page_01.jpg")
```

This means:

```text
start from the folder containing the notebook
→ open archival_images
→ find page_01.jpg
```

### 9.2 Google Colab

If you uploaded `page_01.jpg` using Colab's Files panel, use:

```python
IMAGE_PATH = Path("/content/page_01.jpg")
```

The `/content` folder is Colab's default working area.

> **Choose the version of `IMAGE_PATH` that matches the environment you are using. Do not run both versions one after another.**

Now check that Python can actually find the file:

```python
if not IMAGE_PATH.exists():
    raise FileNotFoundError(f"Could not find {IMAGE_PATH}")
```

### What Does This Check Do?

```python
IMAGE_PATH.exists()
```

asks:

> Does this file actually exist at the location I gave Python?

If the answer is no, Python stops and gives you a clear error rather than failing later during the API request.

If the cell runs without producing an error, Python found your image successfully.

Now display the image:

```python
image = Image.open(IMAGE_PATH)
display(image)
```

This lets you check that you are about to transcribe the correct page.

---

## 10. Create a Function That Sends an Image to Gemini

We could write the full API request every time we want a transcription.

Instead, we will create a **function** called `transcribe_image`.

A function is a reusable block of code.

We give the function:

1. an image path;
2. a prompt;
3. a model.

It sends the image and prompt to Gemini and gives us the model's response.

Run:

```python
def transcribe_image(image_path, prompt, model=MODEL):
    image_path = Path(image_path)

    mime_type, _ = mimetypes.guess_type(image_path.name)

    if mime_type not in {"image/jpeg", "image/png", "image/webp"}:
        raise ValueError(
            "Use a JPEG, PNG, or WebP image for this tutorial."
        )

    image_bytes = image_path.read_bytes()

    image_b64 = base64.b64encode(
        image_bytes
    ).decode("utf-8")

    interaction = client.interactions.create(
        model=model,
        input=[
            {
                "type": "text",
                "text": prompt
            },
            {
                "type": "image",
                "data": image_b64,
                "mime_type": mime_type
            }
        ]
    )

    return interaction.output_text
```

### What Is Each Part Doing?

The first line defines the function:

```python
def transcribe_image(image_path, prompt, model=MODEL):
```

The function expects:

* `image_path` — where the image is stored;
* `prompt` — the transcription instructions;
* `model` — which Gemini model to use.

This line makes sure the file location is represented as a Python `Path`:

```python
image_path = Path(image_path)
```

Next:

```python
mime_type, _ = mimetypes.guess_type(image_path.name)
```

asks Python to identify what kind of image file we are using.

For example:

```text
page_01.jpg
```

will normally be identified as:

```text
image/jpeg
```

The next check makes sure we are using a supported image type:

```python
if mime_type not in {"image/jpeg", "image/png", "image/webp"}:
```

Now Python reads the image itself:

```python
image_bytes = image_path.read_bytes()
```

A computer stores an image as binary data, or **bytes**.

Before those bytes are placed inside this API request, we convert them to **Base64**:

```python
image_b64 = base64.b64encode(
    image_bytes
).decode("utf-8")
```

You do not need to understand the mathematics of Base64 for this tutorial. Its practical purpose here is simple:

> **It converts the image data into a text-safe format that can be transmitted inside the API request.**

Now we reach the actual request:

```python
interaction = client.interactions.create(
    model=model,
    input=[
        {
            "type": "text",
            "text": prompt
        },
        {
            "type": "image",
            "data": image_b64,
            "mime_type": mime_type
        }
    ]
)
```

We are sending Gemini two things together:

```text
our written instructions
+
our archival image
```

Finally:

```python
return interaction.output_text
```

takes the text returned by Gemini and gives it back to whichever part of our notebook called the function.

Once the function exists, we can transcribe an image with the much simpler command:

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

### What Are We Doing Here?

We are saving the transcription instructions inside a variable called:

```text
MINIMAL_PROMPT
```

This allows us to reuse exactly the same wording later.

Now send the image and prompt to Gemini:

```python
minimal_transcription = transcribe_image(
    IMAGE_PATH,
    MINIMAL_PROMPT
)
```

This means:

> Run our `transcribe_image` function using the archival image and the minimal prompt, then save Gemini's response in a variable called `minimal_transcription`.

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

### Why Make the Prompt Longer?

The model still has to interpret the image, but we are now making our **transcription policy** explicit.

For example:

```text
Do not modernize spelling
```

tells the model that historical spelling should be preserved.

```text
Do not silently expand abbreviations
```

tells it not to turn an abbreviated form into a full word without telling us.

```text
If text is illegible, write [illegible]
```

gives it a way to represent uncertainty rather than inventing a confident reading.

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

The longer prompt does **not** guarantee that the transcription will be correct.

Instead, it allows us to investigate an important question:

> **Does giving the model more explicit archival transcription rules change the kinds of errors it makes?**

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

### What Is This Code Doing?

```python
pd.DataFrame(...)
```

creates a table.

Each pair of curly brackets:

```python
{
    "prompt_type": "minimal",
    "transcription": minimal_transcription
}
```

becomes one row.

The resulting table has two columns:

| prompt_type | transcription |
| --- | --- |
| minimal | Gemini's first output |
| archival | Gemini's second output |

Do **not** decide which prompt is better simply by asking which transcription looks more polished.

Compare both outputs against the source image.

A polished transcription may still be less faithful if the model has normalized, omitted, reorganized, or reconstructed the source.

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

### What Is This Code Doing?

The first block creates a Python list containing the names of the evaluation categories.

The second block turns that list into a table with four columns:

```text
category
minimal_score
archival_score
notes
```

The score columns are deliberately empty.

**You**, not the model, fill them in after comparing each transcription against the source image.

The `notes` column is particularly important. A number alone cannot explain what went wrong.

For example, rather than recording only:

```text
named_entities = 1
```

you might write:

```text
Correctly transcribed Bushire but misread the surname in line 4.
```

> **The rubric organizes human judgment; it does not replace human judgment.**

---

## 15. Save the Transcriptions and Record How They Were Produced

If an AI-generated transcription becomes part of a research workflow, saving only the transcription text is not enough.

We should also record:

* which source image was used;
* which prompt was used;
* which model was used;
* when the request was made.

This makes it possible to trace and compare model-generated data later.

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

### What Does This Table Preserve?

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

`to_csv` means:

> Save this pandas table as a CSV file.

```python
index=False
```

prevents pandas from adding an unnecessary numbered index column.

```python
encoding="utf-8-sig"
```

helps preserve multilingual characters when the CSV is opened in commonly used spreadsheet software.

---

## 16. Process Several Images

So far, we have transcribed one page at a time.

One advantage of using an API is that Python can repeat the same workflow across several files automatically.

For this teaching exercise, we will keep the example simple and use **JPG images only**.

For example:

```text
page_01.jpg
page_02.jpg
page_03.jpg
page_04.jpg
```

### 16.1 Choose the Folder Containing the Images

If you are using **Jupyter Notebook or JupyterLab locally** and your images are inside the `archival_images` folder, use:

```python
IMAGE_FOLDER = Path("archival_images")
```

If you are using **Google Colab** and uploaded the images using the Files panel, use:

```python
IMAGE_FOLDER = Path("/content")
```

> **Choose the line that matches the environment you are using. Do not run both versions one after another.**

### 16.2 Ask Python to Find the Images

Run:

```python
image_files = sorted(
    IMAGE_FOLDER.glob("*.jpg")
)

print(f"Found {len(image_files)} image(s).")
```

### What Is This Code Doing?

This part:

```python
IMAGE_FOLDER.glob("*.jpg")
```

means:

> Find every file in that folder whose filename ends in `.jpg`.

The asterisk:

```text
*
```

is a **wildcard**. It means **any filename**.

So:

```text
*.jpg
```

matches files such as:

```text
page_01.jpg
page_02.jpg
letter.jpg
scan_17.jpg
```

This part:

```python
sorted(...)
```

puts the files in alphabetical order.

Finally:

```python
len(image_files)
```

counts how many images Python found.

If your folder contains four JPG files, the output should look like:

```text
Found 4 image(s).
```

You can inspect the list:

```python
image_files
```

A local Jupyter user might see something like:

```text
[
    Path('archival_images/page_01.jpg'),
    Path('archival_images/page_02.jpg'),
    Path('archival_images/page_03.jpg'),
    Path('archival_images/page_04.jpg')
]
```

A Colab user might instead see:

```text
[
    Path('/content/page_01.jpg'),
    Path('/content/page_02.jpg'),
    Path('/content/page_03.jpg'),
    Path('/content/page_04.jpg')
]
```

### 16.3 Create an Empty Place to Store the Results

Before processing the images, create an empty list:

```python
batch_results = []
```

At this point, the list contains nothing.

As Python processes each image, we will add a record containing:

* the filename;
* the model;
* the prompt;
* the transcription;
* the processing time;
* fields for later human review.

### 16.4 Loop Through the Images One at a Time

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

This looks much longer than our earlier code, so read it as a sequence of steps rather than as one large block.

#### Step A: Repeat the Code for Every Image

```python
for image_path in image_files:
```

means:

> Take the first image in `image_files`, run the indented code below it, then move to the second image, then the third, and continue until every image has been processed.

This is called a **loop**.

#### Step B: Tell Us Which File Is Being Processed

```python
print(f"Transcribing: {image_path.name}")
```

prints something like:

```text
Transcribing: page_02.jpg
```

This is useful because API requests may take several seconds.

#### Step C: Send the Image to Gemini

```python
transcription = transcribe_image(
    image_path,
    ARCHIVAL_PROMPT
)
```

uses the same function and archival prompt that we tested earlier.

The only thing changing each time is:

```text
image_path
```

#### Step D: Add the Successful Result to Our List

```python
batch_results.append({...})
```

means:

> Add one new record to `batch_results`.

The record contains the filename, prompt, model, timestamp, and transcription.

We also create two empty fields:

```text
review_status
review_notes
```

so a human reviewer can later record whether the transcription has been checked.

#### Step E: What Do `try` and `except` Do?

Sometimes an API request fails because of:

* an internet connection problem;
* a temporary service error;
* a rate limit;
* an invalid image;
* or another unexpected problem.

Without error handling, one failure could stop the entire loop.

The `try` block says:

> Try to process this image normally.

The `except` block says:

> If that fails, record the error and continue to the next image.

That way, one problematic page does not prevent the remaining pages from being processed.

#### Step F: Pause Briefly

```python
time.sleep(2)
```

asks Python to wait two seconds before sending the next request.

This is a simple precaution for a small teaching exercise. It is **not** a universal rate-limit rule.

For larger projects, check the current Gemini API limits for your model and account.

### 16.5 Turn the Results into a Table

After the loop finishes:

```python
batch_df = pd.DataFrame(batch_results)

batch_df
```

This converts our list of results into a pandas table.

Each image becomes one row.

### 16.6 Save the Batch Transcriptions

Run:

```python
batch_df.to_csv(
    "archival_transcriptions.csv",
    index=False,
    encoding="utf-8-sig"
)

print("Saved archival_transcriptions.csv")
```

You now have a CSV containing the automatically generated transcriptions and information about how each one was produced.

The empty `review_status` and `review_notes` fields allow a human reviewer to record what has been checked.

---

## 17. What Changes When We Scale Up?

Processing 500 pages is not simply the same activity as transcribing one page more quickly.

Automation can make model behavior **systematic** across an entire corpus.

Suppose a model routinely:

* drops marginalia;
* normalizes historical spelling;
* mishandles particular names;
* changes reading order;
* or reconstructs uncertain text confidently.

If we process hundreds of pages automatically, those tendencies can become properties of the dataset itself.

This matters because transcriptions may later be used for:

* keyword search;
* named-entity recognition;
* topic modeling;
* embeddings;
* retrieval-augmented generation;
* quantitative text analysis;
* or structured dataset creation.

An error introduced during transcription can therefore influence later searches or interpretations.

Before scaling an AI transcription workflow:

1. test it on a representative sample;
2. define what counts as an acceptable transcription;
3. inspect recurring failure patterns;
4. decide what level of human review is required.

---

## 18. Adapting the Workflow to Other Multimodal Models

The Python code in this tutorial is specific to Gemini.

The **research workflow is not**.

With another multimodal AI system, you would still need to:

1. authenticate with the service;
2. select a model that accepts images;
3. provide the archival image;
4. provide explicit transcription instructions;
5. receive the model output;
6. save information about how the output was produced;
7. evaluate the transcription against the source.

The exact SDK and API syntax will differ.

This distinction is important:

> **The provider-specific code is replaceable; the decisions about transcription fidelity, provenance, uncertainty, and evaluation are not.**

When comparing different models, keep the source images, prompts, and evaluation criteria as consistent as possible.

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

A natural next tutorial is:

**From AI Transcription to Structured Historical Data**

In that workflow, the reviewed transcription—not the archival image—becomes the input for extracting people, places, dates, offices, and other historical information.

---

## 20. Further Resources

### Gemini API

* [Gemini API documentation](https://ai.google.dev/gemini-api/docs)
* [Getting started with the Gemini API](https://ai.google.dev/gemini-api/docs/get-started)
* [Image understanding](https://ai.google.dev/gemini-api/docs/image-understanding)
* [Using Gemini API keys](https://ai.google.dev/gemini-api/docs/api-key)
* [Gemini API pricing](https://ai.google.dev/gemini-api/docs/pricing)
* [Gemini API rate limits](https://ai.google.dev/gemini-api/docs/rate-limits)

### Related Toolkit Tutorials

* *Getting Started with Jupyter Notebook*
* *Trucial Coast Towns: Building a Historical Gazetteer Dataset with GeoNames*
