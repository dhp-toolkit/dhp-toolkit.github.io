---
title: "Getting Started with Jupyter Notebook"
categories:
  - Beginner
tags:
  - Jekyll
  - update
toc: true
toc_sticky: true
toc_label: "Table of Contents"
---

A hands-on, beginner-friendly tutorial for historians to install and launch Jupyter Notebook, navigate its dashboard, and run your first Python code—complete with clear explanations of key concepts like kernels, cells, and Markdown.

**Learning Objectives:**
* Understand what Jupyter Notebook is and why it’s useful for historians.
* Install the necessary software (Anaconda and Jupyter).
* Launch and navigate the Jupyter Notebook interface.
* Create a new notebook, write your first lines of code, and run a simple “Hello, World!” example.
* Learn basic terminology (e.g., kernel, cell, IDE) so you feel confident exploring further.

**Prerequisites:**

This tutorial assumes a basic understanding of how to work with the Command Line. We strongly recommend that you take the *"Tidy Archives: Using the Command Line to Organize Your Research Files"* tutorial before taking this tutorial.

---

**Need help along the way? Use ChatGPT or any other Generative AI!**

 If you get stuck, don’t worry—many issues are easy to fix. Try asking ChatGPT for help. Just copy-paste the error message or describe what you’re seeing in Jupyter Notebook, and ask something like:

*“I’m trying to install Anaconda on Windows 10 and I see this error: ‘Permission denied when adding to PATH’. How can I fix it?”*

You can also ask general questions like:

*“What is a Jupyter kernel?” or “How do I create a new cell?”*

---

## 1. Introduction

Data analysis and reproducible workflows are becoming increasingly important in digital history. Jupyter Notebook is a tool that lets you combine text, code, and output (tables, charts, etc.) in a single, shareable document. You can annotate your thought process in plain language (using “Markdown” text cells) alongside executable code cells, so that colleagues or students can follow every step of your analysis.

### 1.1 Why Jupyter Notebook?
* **Interactivity:** Run small chunks of code (“cells”) one at a time and immediately see results.
* **Narrative + Code:** Write explanatory text (using Markdown) above or below your code. This is ideal for documenting workflows or teaching.
* **Reproducibility:** Share a single file (.ipynb) that contains both your analysis and your explanations. Anyone with Jupyter installed can run it end to end.


### 1.2 Important Terms
* **Python:** A widely used, beginner-friendly programming language. You’ll run Python code inside Jupyter.
* **Environment:** A self-contained setup of Python and related packages. We’ll use Anaconda, which bundles Python plus Jupyter and many other useful data-analysis libraries.
* **IDE (Integrated Development Environment)**: Software that helps you write and run code. Jupyter Notebook is one example of an IDE, but focused on “notebooks” rather than plain scripts.
* **Kernel:** The process that runs your code behind the scenes. When you execute a cell in a notebook, the kernel interprets and returns results.
* **Cell:** The basic building block in a notebook. There are two primary types: 
- **Markdown cell:** for writing formatted text (headings, bullet lists, links, etc.).
- **Code cell:** for writing executable Python code.

---

## 2. Step-by-Step Technical Instructions

### 2.1 Install Python and Jupyter (Using Anaconda)

Anaconda is a free package that installs Python, Jupyter Notebook, and dozens of commonly used libraries in a single download. It’s the easiest way to get started, especially if you’ve never installed Python before.

**conda** is the package manager that comes with Anaconda. You can install additional Python packages with ```conda install <package> or pip install <package>```.

**Instructions:**

1. Download **Anaconda**
* Go to the Anaconda website: [https://www.anaconda.com/products/distribution](https://www.anaconda.com/products/distribution){:target="_blank" rel="noopener"} 
* Select the **Python 3.x** version for your operating system.
* Click “Download” and wait for the installer to finish downloading.

2. Run the Installer
* On **Windows**: Double-click the ```.exe``` file you downloaded.
* On **macOS**: Double-click the ```.pkg``` file downloaded.
* Proceed through the prompts, accepting the default settings unless you have a reason to change them.
* When it asks whether to add Anaconda to your system PATH, say “Yes” (Windows) or accept the default (macOS/Linux).

3. Verify Installation
* Open a new Terminal (macOS/Linux) or Anaconda Prompt (Windows).
* Type: ```conda --version```
* You should see something like ```conda x.x.x``` if the installation was successful.

![Terminal window](/assets/images/jupyter/tutorial-jupyter-2.1.png)

### 2.2 Launch Jupyter Notebook

On Windows or macOS, open **Anaconda Navigator** from your **Start Menu** (Windows) or **Applications folder** (macOS).

![Anaconda Navigator Main Page](/assets/images/jupyter/tutorial-jupyter-2.2.png)

In Anaconda Navigator, locate **Jupyter Notebook** and click the **Launch** button.

This will automatically launch your terminal with a script to launch the Jupyter notebook and open a webpage in your default web browser showing the **Jupyter Notebook Dashboard** (often the link starts with ```http://localhost:8888/```).

![Terminal prompt that gets launched](/assets/images/jupyter/tutorial-jupyter-2.3.png)

**What Is Jupyter Dashboard?**

![Terminal prompt that gets launched](/assets/images/jupyter/tutorial-jupyter-2.4.png)

When you launch Jupyter Notebook, your browser opens the **Jupyter Dashboard**, which is simply a view of your computer’s local files and folders at the location where you started Jupyter. From here, you can:

* **See Your Local Files/Folders:** The main area lists everything in the current directory on your computer.
* **Navigate Between Directories:** Click on folder names to move into subfolders, or click “..” to go up a level. This lets you explore any location on your system.
* **Create Notebooks Anywhere:** Once you’ve navigated to the folder where you want to work, click New → Python 3 (or another option) in the top-right to create a notebook that will be saved in that folder and locally in your computer (see below for detailed instructions on how to create a new notebook). You can also create text files, new folders, or open a Terminal from here.
* **Monitor Running Sessions:** Click the Running tab to view and shut down any open notebooks or terminals.

#### 2.2.1 How to Save a Notebook in a Different Folder

Launching Jupyter Notebook from Anaconda will open the Dashboard in your **user’s home directory** by default (e.g., ```C:\Users\YourUsername``` on Windows or ```/Users/YourUsername on macOS/Linux```).

If you want to save your notebooks in a different directory: 
1. Create Your Folder
In your operating system’s file manager (Finder on macOS or File Explorer on Windows), make a new folder—e.g., ```MyJupyterNotebooks```—wherever you like (Desktop, Documents, etc.).

2. Launch Jupyter from Anaconda Navigator
Open **Anaconda Navigator** and click **Launch** under “Jupyter Notebook”. By default, the Dashboard will show your home directory.

3. Navigate to Your Folder in the Dashboard
In the Dashboard’s file list, click into the folder you created (e.g., ```MyJupyterNotebooks```). Now, any notebook you open or create here will automatically be saved in that folder.

### 2.3. Create and Navigate a New Notebook

**1. Create a New Notebook**

In the Dashboard, click **New → Python 3 (pykernel)**. This will open a new browser tab with a notebook named **Untitled**.

![New notebook](/assets/images/jupyter/tutorial-jupyter-2.5.png)

You can click on “Untitled” to rename the notebook. For example, rename it to ```hello_world.ipynb```.

**2. Interface Overview**

**Menu Bar** (File, Edit, View, Insert, Cell, Kernel, etc.)

Provides dropdown menus for common actions such as:
- creating or saving notebooks (File), 
- undo/redo (Edit), 
- toggling line numbers (View), 
- inserting new cells (Insert), 
- running or restarting the notebook’s computational engine (Kernel), 
- managing individual cells (Cell).

**Toolbar** (Save, Add Cell, Run, Stop, Restart, etc.)

Contains buttons for quick access to frequent tasks:

- 💾 Save: Save the current notebook locally in the directory specified in the Dashboard.
- ➕ Add Cell: Insert a new cell below the selected one.
- ▶️ Run: Execute the selected cell.
- ⏹️ Stop: Interrupt a running cell.
- 🔄 Restart: Restart the kernel (clear all computations).

**Code/Markdown Cells** (empty boxes awaiting input)
* **Code Cells:** Write and execute Python code. When you run a code cell, the kernel processes it and displays output directly beneath.
* **Markdown Cells:** Write formatted text (headings, lists, links, etc.) using Markdown syntax. When run, they render as formatted notes or explanations.

**3. Add and Run Cells**

* By default, you’ll see one empty cell labeled ```In [ ]:```. This is a **code cell**. Click inside the cell and type: ```print("Hello, World!")``` (or any other instruction)
* To run the cell, do one of the following:
- Press``` Shift + Enter``` (runs the current cell and moves to the next one).
- Click the **▶️ Run** button in the toolbar.
* After running, you should see the text ```“Hello, World!”``` displayed directly below the cell. The prompt will update (e.g., ```In [1]:```) to show that it has run once.

![Hello, World example](/assets/images/jupyter/tutorial-jupyter-2.6.png)


**4. Insert a New Markdown Cell**
* Click on the **+** (plus) button in the toolbar to insert a new cell below.
* By default, it’s a code cell. Switch it to **Markdown** by clicking on the **dropdown** that says “Code” and selecting Markdown. The dropdown can be located here:

![Dropdown location](/assets/images/jupyter/tutorial-jupyter-2.7.png)

*  Markdown cells follow the usual Markdown syntax. For example, you can paste this text there:

```ruby
### What Just Happened?  
We wrote a simple Python command: `print("Hello, World!")`.  
- `print(...)` is a Python **function** that displays text in the output.  
- Everything inside the quotes (`"Hello, World!"`) is a **string**, which is just a sequence of characters (text). 
```
* Run the Markdown cell by clicking on the **Run** button or pressing ```Shift + Enter```. You’ll see the formatted text in that cell.

![Output after running the above instruction](/assets/images/jupyter/tutorial-jupyter-2.8.png)

### 2.4. Save and Shut Down the Notebook

**1. Save Your Notebook**
* Click the ***💾 (“Save”)*** button on the toolbar or press ```Ctrl + S``` (```Cmd + S``` on macOS).
* You’ll notice a small **“*”** next to the filename disappear when it’s fully saved.

> **Note:** Jupyter Notebook has an autosave feature enabled by default, but it typically autosaves your work every 120 seconds (2 minutes). You'll often see a status message at the top of the notebook like ```Last Checkpoint: a few seconds ago (autosaved)``` or ```Last Checkpoint: 2 minutes ago (unsaved changes)``` reflecting the last time it was saved.
However, it is still recommended to manually save your work by running Ctrl + S on Windows or Cmd + S on Mac as this is more immediate than waiting for the autosave interval.


**2. Close and Shut Down the Notebook**
* To close just the browser tab, click the **×** on the tab.
* But this alone doesn’t stop the kernel. To shut down the kernel, go back to the Dashboard tab, find your ```hello_world.ipynb``` under the **Running** tab, select it, then click **Shutdown** (this button is located on the toolbar on top of the Dashboard)

![Shutting Down the Notebook](/assets/images/jupyter/tutorial-jupyter-2.9.png)

---

## 3. Extensions for Advanced Users

Once you’ve mastered the basics above, here are the next steps you can explore:

### Markdown Formatting

Write headings (```# Heading 1, ## Heading 2```), bullet lists (```- item```), and even embed images. Historians can use this to document sources and add annotations directly in the Notebook.

### Installing Additional Packages

If you want to analyze CSV files or create charts, you can install libraries such as **pandas** and **matplotlib** by running in the terminal: ```conda install pandas matplotlib```

Then in a code cell inside your **Jupyter Notebook**:

```ruby
import pandas as pd
import matplotlib.pyplot as plt
```

These libraries let you import data, manipulate it, and visualize it—all within the same document.

### Using Jupyter Extensions

**nbextensions**: Add features like spell-checking or code folding. 

To install:

```ruby
conda install -c conda-forge jupyter_contrib_nbextensions
jupyter contrib nbextension install --user
```

Once installed, you’ll have a new **“Nbextensions”** tab in the Dashboard to turn on helpful tools.

### Sharing Notebooks

Export your notebook as a PDF or HTML via **File → Download as → PDF** (requires extra setup) or **HTML** (no extra installation).
People you shared it with would be able to view your workflow without running any code.

---

## 4. Keyboard Shortcuts to Speed Up Workflow

* **Enter**: Edit the current cell.
* **Esc**: Command mode (navigate between cells).
* **Shift + Enter**: Run current cell and move to the next.
* **Ctrl + Enter**: Run current cell and stay in it.
* **A** (in Command mode): Insert a cell above.
* **B** (in Command mode): Insert a cell below.
* **M** (in Command mode): Convert cell to Markdown.
* **Y** (in Command mode): Convert cell to Code.
* **DD** (press D twice, in Command mode): Delete the current cell.

---

## 5. Troubleshooting & Tips

Below are some common issues you might encounter when working with Jupyter Notebook and suggested solutions:

### 5.1. Notebook Won’t Launch

**Issue:** Typing ```jupyter notebook``` in the Terminal/Anaconda Prompt doesn’t open a browser window or gives an error.

**Possible Causes & Fixes:** 
* You’re in a directory with no write permissions. Try running ```jupyter notebook``` from your Home or Documents folder.
* Another process is already using port 8888. Run ```jupyter notebook --port=8890``` (or any free port).
* Environment variables didn’t update after installation. Close and reopen your Terminal/Anaconda Prompt before retrying.

### 5.2 Kernel Issues (e.g., “Kernel Busy,” “No Kernel Found”)

**Issue:** The circle next to “Kernel” stays solid (busy) or you see “No Kernel” in the top right.

**Possible Causes & Fixes:**
* The Python environment in your system PATH doesn’t match the one Jupyter expects. In Anaconda Prompt (Windows) or a Terminal (macOS/Linux), activate the base environment with ```conda activate base``` before launching.
* You installed Jupyter without activating its environment—always run ```conda activate base``` (or your chosen environment) first.
* If you previously created another environment (e.g., ```conda create -n hist_env python=3.9```), you must run ```conda activate hist_env``` and then ```conda install jupyter``` within that environment.


### 5.3 “Module Not Found” Errors

**Issue:** Running ```import pandas as pd``` shows ```ModuleNotFoundError: No module named 'pandas'```.

**Fix:** Install the missing package by running either: ```conda install pandas```  or
```pip install pandas``` in your active environment. Then restart the kernel (Kernel → Restart).


### 5.4 Notebook Autosaves Too Frequently or Not at All

**Tip:** Jupyter autosaves every few minutes by default. You can change autosave settings under File → Save and Checkpoint or adjust in View → Cell Toolbar → Edit Metadata. If Jupyter isn’t autosaving, manually save often (```Ctrl + S``` / ```Cmd + S```).

### 5.5 Browser Doesn’t Show Images or Plots

**Issue:** You run plotting code (e.g., with ```matplotlib```) but see no figure.

**Fix:** Ensure you include ```%matplotlib``` inline at the top of your notebook. This “magic command” tells Jupyter to display plots in the output cells.

### 5.6 Accidentally Deleted a Cell:

**Tip:** In Command mode, press Z to undo deleting a cell. This only works until you close the notebook.

---

## 6. Further Resources

To continue learning, explore these beginner-friendly guides and references:

**1. Official Jupyter Documentation**

* Jupyter Project: [https://jupyter.org/](https://jupyter.org/){:target="_blank" rel="noopener"} 

* User Guide (covers notebooks, JupyterLab, widgets):[https://jupyter.readthedocs.io/en/latest/](https://jupyter.readthedocs.io/en/latest/){:target="_blank" rel="noopener"} 

**2. Anaconda Documentation**

* Installation and managing environments:[https://docs.anaconda.com/anaconda/](https://docs.anaconda.com/anaconda/){:target="_blank" rel="noopener"}

**3. Python Basics**

* Official Python Tutorial (beginner level):[https://docs.python.org/3/tutorial/](https://docs.python.org/3/tutorial/){:target="_blank" rel="noopener"}

* W3Schools Python Tutorial (interactive examples):[https://www.w3schools.com/python/](https://www.w3schools.com/python/){:target="_blank" rel="noopener"}

**4. Markdown Cheat Sheet**

* Basic Markdown syntax for formatting text cells:[https://www.markdownguide.org/cheat-sheet/](https://www.markdownguide.org/cheat-sheet/){:target="_blank" rel="noopener"}

**5. Data Analysis with Python**

* “Pandas Documentation: Getting Started” (for tabular data):[https://pandas.pydata.org/docs/getting_started/index.html](https://pandas.pydata.org/docs/getting_started/index.html){:target="_blank" rel="noopener"}

* “Matplotlib: Visualization With Python” (basic plotting):[https://matplotlib.org/stable/tutorials/introductory/pyplot.html](https://matplotlib.org/stable/tutorials/introductory/pyplot.html){:target="_blank" rel="noopener"}

**6. Forums & Help Sites**

* Stack Overflow: Search for “Jupyter Notebook [your error message]”.

* Jupyter Community Forum: [https://discourse.jupyter.org/](https://discourse.jupyter.org/){:target="_blank" rel="noopener"}