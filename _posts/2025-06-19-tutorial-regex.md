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

## Why Preprocess Text?

Before we analyze text (like searching, summarizing, or finding patterns), we need to clean it to ensure consistency, remove unnecessary parts (like punctuation or symbols), and help us focus on the actual content.

## Step 1: Load the Text File

Open the `.txt` file and read its contents into a Python string variable so we can work with it.

```ruby
# Open and read the contents of the file
with open('AgreementWithTrucialChiefs_Sample.txt', 'r', encoding='utf-8') as f:
    raw_text = f.read()

# Preview first 500 characters
print(raw_text[:500])
```

![Output](/assets/images/regex/tutorial-regex-1.1.png)

