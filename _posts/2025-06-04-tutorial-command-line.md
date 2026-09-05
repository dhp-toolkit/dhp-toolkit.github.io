---
title: "Tidy Archives: Using the Command Line to Organize Your Research Files"
categories:
  - Beginner
tags:
  - Post Formats
  - notice
toc: true
toc_sticky: true
toc_label: "Table of Contents"
---

A hands-on, beginner-friendly guide to organizing digital research materials using the command line. Learn how to navigate folders, rename and move files, and apply consistent naming practices using simple Bash commands—all within a realistic sample archive. No coding experience required; perfect for historians and humanities students managing scanned documents, PDFs, and notes.


**Target Audience:** Humanities students or faculty with no coding background but an interest in managing digital research materials (especially scanned documents, PDFs, notes, etc.)

**Learning Objectives:** 
By the end of this module, learners will:
* Understand the importance of structured file and folder naming
* Use basic command-line tools (macOS/Linux and Windows) to view, create, rename, and move files
* Apply these tools to organize a sample digital research archive
* Reflect on best practices for long-term research file management

---

## 1. Introduction 

Historians work with dozens—sometimes thousands—of files: PDFs, scans, photos, notes, spreadsheets. Keeping them organized is essential, especially when working collaboratively or returning to a project after months.
This module introduces a set of best practices and command-line tools to manage your research archive efficiently.

---

## 2. Preparing Your Terminal

### 2.1 macOS / Linux:
Open the Terminal app (search “Terminal” in Spotlight or locate it in the Applications folder). On current versions of macOS, the default shell is usually `zsh`; many Linux systems use `bash`. The basic commands used in this tutorial—such as `pwd`, `ls`, `cd`, `mkdir`, `mv`, `cp`, and `cat`—work the same way in both shells.

![Terminal icon](/assets/images/cmdline/tutorial-cmdline-2.png)

### 2.2 Windows:

Install [Git for Windows](https://gitforwindows.org/){:target="_blank" rel="noopener"} and use **Git Bash**. It lets you use Bash commands on Windows. After installing, open Git Bash from the Start menu.

Alternative: Windows users can also work in PowerShell, which is included with Windows. However, some commands and syntax differ from the Bash examples used in this tutorial. To follow the instructions exactly as written, we recommend using Git Bash.

## 3. Download the Sample Dataset
For this tutorial, we will be working with a sample dataset, which can be downloaded [here](/assets/sample-archive.zip). The dataset folder is organized as follows:

```
sample-archive/
├── scans/
│   ├── IMG_001.jpg
│   ├── IMG_002.jpg
│   └── scan final 003.jpg
├── notes/
│   ├── meeting1.txt
│   └── draft_notes.docx
├── pdfs/
│   ├── article-A.pdf
│   └── B-article.pdf
└── metadata.csv
```

![Sample-archive folder structure](/assets/images/cmdline/tutorial-cmdline-3.1.png)

## 4. Step-by-Step Activities: Bash Basics

Before we begin typing commands, it's essential to understand the idea of a **path**.

**What is a path?**

A **path** is the location of a file or folder on your computer. Bash commands use paths to know **where** to find or place things. Without the correct path, the command won’t know what to act on.

There are two types of paths:
* **Absolute path** — starts from the root of your system
 Example: ```/Users/username/Desktop/sample-archive/scans/IMG_001.jpg```

* **Relative path** — starts from your current location
Example: ```scans/IMG_001.jpg``` (if you're already inside the sample-archive folder)

**Why you need it:**

All Bash commands that involve moving, copying, or opening files need to know where those files are—this is done by specifying their **path**.

One of the most common commands you'll use is: ```cd foldername/```

This command means **change directory**—in other words, move into a different folder.
 For example, the command: ```cd images/``` only works if your current directory contains a folder called **images**. If you're in the wrong place, the command will fail.

All commands in the next sections assume you’ve already extracted the ```sample-archive``` folder and are now working inside that folder. To follow along, open your terminal shell, then cd into that folder: ```cd path/to/sample-archive```. 

> **TIP**: Not sure what the path to your ```sample-archive``` folder is?
1. Open your Terminal and type cd (with a space after).
2. Then drag the ```sample-archive``` folder into the Terminal window.
3. Press Enter. This will paste the full path automatically.

![How to navigate to the sample-archive directory via terminal](/assets/images/cmdline/tutorial-cmdline-4.1.png)

### 4.1 Navigating

**What you will learn:** how to move around the file system using basic navigation commands. You'll know commands to check what’s inside folders, switch between directories, and confirm your directory.

**Command Line Prompt:**
To follow along, run the following commands in the specified order.  

```
pwd 
ls             
cd scans         
cd ..      
```
**Explanation:**

| Command | What it does | Result  |
| ----------- | ----------- | ----------- |
| ```pwd``` | Prints the full path of the current directory. | Confirm you are in .../sample-archive |
| ```ls```| Lists files and folders in the current directory. | The output should include ```scans```, ```notes```, ```pdfs```, and ```metadata.csv```. |
| ```cd scans``` | Changes into the scans directory. | Prints nothing; can run ```ls``` to print content of the ```scans``` folder |
| ```cd ..``` | Moves one directory up | Move to the directory above ```scans```, which is ```sample-archive``` |

**Example:**

Below is an example of running these commands in a Mac Terminal. Your command prompt may look different depending on your computer and shell. In this screenshot, each line begins with ```(base) user-name current-directory %```. You only need to type the command that appears after the prompt.

![Example output](/assets/images/cmdline/tutorial-cmdline-4.2.png)

### 4.2 Creating and Moving Files

**What you will learn:** how to make folders, move files into them, and make copies. These skills help keep your archive tidy and organized.

> **Practice safely:** While learning these commands, work with the sample archive or a copy of your research files rather than your only copy. Commands such as `mv` change the location or name of files immediately.

**Command Line Prompt:**
```
pwd 
mkdir images 
mv scans/IMG_001.jpg images/
cp pdfs/article-A.pdf pdfs/article-A-COPY.pdf
```

**Explanation:**

| Command | What it does | Result  |
| ----------- | ----------- | ----------- |
| ```pwd``` | Check your current directory. | Verify your current directory is ```sample-archive```. If not, you can move one directory up or down using the commands ```cd ..``` or ```cd directory-name```, respectively (example is shown below).|
| ```mkdir images```| Make a new folder named images inside the current directory. | A new ```images``` directory appears when you run ```ls```. |
| ```mv scans/IMG_001.jpg images/``` | Move IMG_001.jpg into ```images/``` folder. | If this command runs successfully, nothing should appear on the terminal output. You can verify the output by manually checking images folder or running cd images then running ```ls```. |
| ```cp pdfs/article-A.pdf pdfs/article-A-COPY.pdf``` | Create a duplicate of ```article-A.pdf``` with a new name of ```article-A-COPY.pdf``` in the same folder. | If this command runs successfully, nothing should appear on the terminal output. You can verify the output by manually checking pdfs folder or running ```cd pdfs``` then running ```ls```. |

**Example:**

![Example output](/assets/images/cmdline/tutorial-cmdline-4.3.png)

**Example:** How to move one directory up and down using cd

![Example output](/assets/images/cmdline/tutorial-cmdline-4.4.png)

### 4.3 Gather & Merge Text Notes

***What you will learn:*** how to collect all ```.txt``` files into one place and merge them into a single combined notes file using a new command: ```cat```

 You can do this manually, or from inside the ```sample-archive``` directory by running ```touch notes/meeting2.txt```.

This command creates a new empty file called ```meeting2.txt``` inside the ```notes``` folder. You can then open the file and add any text you like.


**Command Line Prompt:**
```
pwd
mkdir all_notes
mv notes/*.txt all_notes/
cat all_notes/*.txt > all_notes/combined_notes.txt 
```

**Explanation:**

| Command | What it does | Result  |
| ----------- | ----------- | ----------- |
| ```pwd``` | Check your current directory. | Verify your current directory is ```sample-archive```. If not, you can move one directory up or down using the commands ```cd ..``` or ```cd directory-name```, respectively. |
| ```mkdir all_notes```| Create a new directory called ```all_notes```. | If this command runs successfully, nothing should appear on the terminal output, and ```all_notes``` should appear in the current directory when running ```ls```. |
| ```mv notes/*.txt all_notes/``` | Move all .txt files from ```notes/``` to ```all_notes/```. | If this command runs successfully, nothing should appear on the terminal output. You can verify that the .txt files are in ```all_notes/``` by running ```cd all_notes``` then ```ls```. |
| ```cat all_notes/*.txt > all_notes/combined_notes.txt``` | Combine all text files into one master text file. | If this command runs successfully, nothing should appear on the terminal output and a new file ```combined_notes.txt``` is created containing all the text. |

**Example:**

![Example output](/assets/images/cmdline/tutorial-cmdline-4.5.png)

### 4.4 Renaming a File

**What you will learn:** how to rename files one-by-one for consistent naming rules. This is especially useful for applying chronological order or standardized formats to scanned items.

**Command Line Prompt:**
```mv "scans/scan final 003.jpg" "scans/IMG_003.jpg"```

> ***Note:*** The command ```mv``` can be used not only for moving files but also for renaming individual files.

**Explanation:**

| Command | What it does | Result  |
| ----------- | ----------- | ----------- |
|  ```mv "scans/scan final 003.jpg" "scans/IMG_003.jpg"``` | Renames the file ```scan final 003.jpg``` inside the scans directory to ```IMG_003.jpg``` | If this command runs successfully, nothing should appear on the terminal output, and ```scan final 003.jpg``` should be renamed to ```IMG_003.jpg```. |

> **Note:** Because your source filename ```scan final 003.jpg``` has spaces, you **need to** enclose it in double quotes.

**Example:**

![Example output](/assets/images/cmdline/tutorial-cmdline-4.6.png)


### 4.5 File Naming Best Practices

| Suggestion | Example |
| ----------- | ----------- |
| Use lowercase only | ```letter1900_final.txt``` |
| Avoid spaces or special characters | ```letter_1900_draft.pdf```, not ```Letter (Final) 1900.pdf``` |
| Use hyphens or underscores, not spaces | ```notes-meeting1.txt``` |
| Start with date if chronological | ```1900-05-12_letter.pdf``` |
| Be consistent | If you use underscores, use them everywhere |

>**Apply what you learned:** Pick messy files from sample-archive and rename them using these practices.

---

## 5. How to debug with generative AI

When something goes wrong in the Terminal, you can use ChatGPT or another AI tool to help fix the problem. But you’ll only get useful help if you provide the right context.

> **Privacy and security:** Before sharing an error message or screenshot with a generative AI tool, check that it does not contain passwords, API keys, private repository content, personal information, or restricted research data. Never paste authentication credentials into a chatbot.
> 
**What to include in your prompt:**
1. Your operating system and shell
Example: Windows 11 with Git Bash, or macOS 14 using zsh in Terminal
2. The command you ran
Paste it exactly as you typed it.
3. The full error message
Copy/paste everything the Terminal printed out in response.
4. Your goal
What are you trying to do? Briefly explain it in one sentence.
5. (Optional) What you tried so far, and what happened?


**Example Error:**

![Example error](/assets/images/cmdline/tutorial-cmdline-5.1.png)

**Example Prompt:**

I’m using macOS Terminal inside the ```sample-archive/``` folder. I tried running this command: ```mv IMG_001.jpg scans/```
But I got this error: ```mv: rename IMG_001.jpg to scans/IMG_001.jpg: No such file or directory```
I’m trying to move one image from the images/ folder into a scans/ folder. What’s wrong with this command and how should I fix it?

**ChatGPT response:**

![Example error](/assets/images/cmdline/tutorial-cmdline-5.2.png)

**Fixed prompt:**

![Example output](/assets/images/cmdline/tutorial-cmdline-5.3.png)


## 6. Further Resources
* Kate Thornhill & Gabriele Hayden, “Making File Names for Digital Exhibits”
* Programming Historian: [Introduction to the Bash Command Line](https://programminghistorian.org/en/lessons/intro-to-bash)
