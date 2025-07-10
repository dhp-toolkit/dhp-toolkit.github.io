---
title: "Text Preprocessing with Regex in Python"
categories:
  - Intermediate
tags:
  - chat
  - Post Formats
toc: true
toc_sticky: true
toc_label: "Table of Contents"
---

In this tutorial, we’ll walk through how to preprocess a historical document using **Python**. Our example is a document titled `AgreementWithTrucialChiefs_Sample.txt`, which includes both English (Latin alphabet) and Arabic script.

You'll learn how to:
- Load a .txt file
- Make all letters lowercase
- Remove punctuation
- Remove extra spaces
- Remove special characters
- Choose to keep either Arabic or Latin (English) text only

**Prerequisites:**

This tutorial assumes a basic understanding of how to work with Jupyter Notebook and an ablity to write and follow-through a code written in Python. We strongly recommend that you take the `Getting Started with Jupyter Notebook` tutorial before taking this tutorial.

**Before you begin:**

You can follow this tutorial in one of two ways:

- Download and run the notebook directly on your computer using Jupyter (e.g., through Anaconda or any Python environment that supports notebooks), or
- Create a new Jupyter notebook and copy-paste the code and explanations step-by-step as you follow along. This lets you write and test your own code interactively.

Choose whichever method you’re most comfortable with — both will work just fine!

The **ready-to-run** Jupyter notebook can be downloaded following this [link](){:target="_blank" rel="noopener"}. 

> To download the file following the above link, click on the 3 dots on the upper-right corner which displays **'More file actions'**, then click on the **Download** button.
![Screenshot of the download button](/assets/images/geonames/tutorial-geonames-1.1.png)


## 1. Introduction

Before we analyze text (like searching, summarizing, or finding patterns), we need to clean it to ensure consistency, remove unnecessary parts (like punctuation or symbols), and help us focus on the actual content.

## 2. Load the Text File

Open the `.txt` file and read its contents into a Python string variable so we can work with it.

```ruby
# Open and read the contents of the file
with open('AgreementWithTrucialChiefs_Sample.txt', 'r', encoding='utf-8') as f:
    raw_text = f.read()

# Preview first 500 characters
print(raw_text[:500])
```

![Output](/assets/images/regex/tutorial-regex-1.1.png)

