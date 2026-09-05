---
title: "Getting Started with Jupyter Notebook"
categories:
  - Beginner
tags:
  - Jupyter
  - Python
toc: true
toc_sticky: true
toc_label: "Table of Contents"
---

A hands-on, beginner-friendly tutorial for historians who want to begin working with computational notebooks. You will install and launch Jupyter Notebook, learn how notebooks organize code and explanatory text, and run your first Python commands. Along the way, the tutorial introduces key concepts such as kernels, environments, cells, and Markdown.

**Learning Objectives:**

By the end of this tutorial, you will be able to:

* Explain what a Jupyter Notebook is and why notebooks are useful for historical research and teaching.
* Install Python and Jupyter using Anaconda.
* Launch and navigate the Jupyter Notebook interface.
* Create, rename, save, and reopen a notebook.
* Write and run simple Python code.
* Add explanatory text using Markdown cells.
* Understand basic terminology such as environments, kernels, and cells.
* Identify some basic practices for making notebook-based research more reproducible.

**Prerequisites:**

No previous Python experience is required. Some familiarity with folders, file paths, and the command line is helpful but not essential. If you would like an introduction to those concepts first, see the *Tidy Archives: Using the Command Line to Organize Your Research Files* tutorial.

> **Last reviewed:** September 2026

---

## 1. Introduction

Data analysis and reproducible workflows are increasingly important in digital history. A Jupyter Notebook is a computational document that allows you to combine explanatory text, executable code, data, and outputs such as tables and charts in a single file.

Instead of keeping your code in one place and your explanation of what you did somewhere else, a notebook allows you to document the steps of an analysis alongside the commands that produced the results. This makes notebooks especially useful for teaching, exploratory research, documenting workflows, and sharing computational methods.

Notebook files normally use the extension ```.ipynb```.

### 1.1 Why Use Jupyter Notebooks?

* **Interactivity:** Run small sections of code, called cells, one at a time and immediately see the result.
* **Narrative + Code:** Place explanatory text, source notes, methodological comments, or interpretation directly alongside your code.
* **Transparency:** Readers can see not only the final result but also the steps used to produce it.
* **Reproducibility:** A notebook can preserve code and explanations together so that you—or someone else—can rerun the analysis later.
* **Teaching:** Students can work through code incrementally without needing to understand an entire program at once.

> **Important:** A notebook improves reproducibility, but the ```.ipynb``` file alone does not preserve everything needed to recreate an analysis. Data files, software versions, and installed Python packages also matter. We return to this issue later in the tutorial.

### 1.2 Jupyter Notebook and JupyterLab

Project Jupyter provides several interfaces for working with notebooks.

**Jupyter Notebook** provides a streamlined, document-centered interface for creating and working with individual notebooks.

**JupyterLab** provides a broader workspace where you can open notebooks, data files, terminals, text files, and other resources side by side.

This tutorial uses **Jupyter Notebook** because its focused interface is particularly suitable for beginners. The core skills you learn here—working with cells, kernels, Markdown, and ```.ipynb``` files—also transfer to JupyterLab.

### 1.3 Important Terms

* **Python:** A widely used programming language. We will run Python code inside our notebook.
* **Notebook:** A document, usually saved as an ```.ipynb``` file, that can contain code, explanatory text, and computational output.
* **Environment:** An isolated collection of Python and installed software packages. Different projects can use different environments to avoid conflicts between package versions.
* **conda:** A tool for managing software packages and Python environments. It is included with Anaconda.
* **Kernel:** The computational process that executes the code in your notebook. When you run a Python cell, the Python kernel processes the instructions and returns the result.
* **Cell:** A block within a notebook. The two types you will use most frequently are:
  * **Code cell:** contains executable Python code.
  * **Markdown cell:** contains formatted explanatory text, headings, links, lists, and notes.

---

## 2. Step-by-Step Technical Instructions

### 2.1 Install Python and Jupyter Using Anaconda

There are several ways to install Python and Jupyter. For this beginner tutorial, we will use **Anaconda Distribution** because it provides Python, the ```conda``` environment manager, Jupyter Notebook, JupyterLab, and many commonly used data-analysis packages in a single installation.

> **No-install option:** If you only want to experiment with notebooks before installing anything, Project Jupyter also provides browser-based demonstrations through its “Try Jupyter” service. For sustained research work, however, a local installation gives you more control over your files, packages, and environments.

#### 1. Download Anaconda

* Go to the Anaconda Distribution download page: [https://www.anaconda.com/download](https://www.anaconda.com/download){:target="_blank" rel="noopener"}
* Select the installer appropriate for your operating system and computer.
* Download the installer.

#### 2. Run the Installer

* On **Windows**, open the downloaded installer and follow the installation prompts.
* On **macOS**, open the downloaded ```.pkg``` installer and follow the installation prompts.
* Unless you have a specific reason to change them, use the recommended installation settings.

> **Windows users:** If the installer gives you an option to add Anaconda to the Windows ```PATH``` environment variable, leave this option **unchecked**. Anaconda recommends using **Anaconda Prompt** or **Anaconda Navigator** rather than permanently adding Anaconda to the Windows PATH.

#### 3. Verify the Installation

Open **Anaconda Prompt** on Windows or a **Terminal** on macOS/Linux and run:

```bash
conda --version
```

You should see a version number, for example:

```text
conda 25.x.x
```

The exact version number will depend on when you installed Anaconda.

![Terminal window](/assets/images/jupyter/tutorial-jupyter-2.1.png)

### 2.2 Launch Jupyter Notebook

The easiest way for beginners to launch Jupyter is through **Anaconda Navigator**.

1. Open **Anaconda Navigator** from the Start Menu on Windows or the Applications/Launchpad interface on macOS.
2. Locate **Jupyter Notebook**.
3. Click **Launch**.

![Anaconda Navigator Main Page](/assets/images/jupyter/tutorial-jupyter-2.2.png)

Anaconda will start a local Jupyter server and open Jupyter Notebook in your default web browser. The address often begins with something similar to:

```text
http://localhost:8888/
```

Although you are viewing Jupyter in a web browser, the notebook is running **locally on your computer**. The ```localhost``` address refers to your own machine; it does not mean that your files have automatically been published on the internet.

![Terminal prompt that gets launched](/assets/images/jupyter/tutorial-jupyter-2.3.png)

> **Interface note:** Jupyter is actively developed, so buttons, icons, and menu labels may look slightly different depending on the version installed on your computer. The underlying concepts and workflow remain the same.

#### What Is the Jupyter Dashboard?

![Jupyter Dashboard](/assets/images/jupyter/tutorial-jupyter-2.4.png)

When Jupyter Notebook opens, you will normally begin in the **Dashboard**. The Dashboard allows you to browse files and folders accessible from the location where Jupyter was started.

From here, you can:

* **See files and folders:** Browse the directories available to Jupyter.
* **Navigate between directories:** Click folder names to move into them.
* **Create notebooks:** Navigate to the folder where you want your notebook to be stored and create a new notebook there.
* **Open existing notebooks:** Click an ```.ipynb``` file to open it.
* **Monitor running sessions:** Use the Running section or tab to see notebooks, terminals, and kernels that are still active.

#### Alternative: Launch from the Command Line

If you are comfortable with the command line, you can also navigate to the folder in which you want to work and run:

```bash
jupyter notebook
```

For example:

```bash
cd path/to/MyJupyterNotebooks
jupyter notebook
```

This opens Jupyter with that folder as your starting location.

### 2.2.1 Choose Where to Save Your Notebook

Before creating a notebook, decide where you want the file to live.

For example, create a folder called:

```text
MyJupyterNotebooks
```

You can create this folder using Finder on macOS or File Explorer on Windows.

Then:

1. Launch Jupyter Notebook.
2. Navigate through the Dashboard until you reach ```MyJupyterNotebooks```.
3. Create your notebook from inside that folder.

The resulting ```.ipynb``` file will be stored there.

> **Research practice:** For a real project, create a clearly named project directory and keep your notebooks, data, documentation, and outputs organized rather than saving notebooks in unrelated folders around your computer.

### 2.3 Create and Navigate a New Notebook

#### 1. Create a New Notebook

In the Dashboard, use the **New** button or notebook-creation option and select a **Python 3** kernel.

Depending on your version, the exact wording may differ slightly—for example, you may see **Python 3**, **Python 3 (ipykernel)**, or be asked to select a kernel after creating the notebook.

![New notebook](/assets/images/jupyter/tutorial-jupyter-2.5.png)

A new notebook will open.

Rename it to:

```text
hello_world.ipynb
```

A descriptive filename is preferable to leaving notebooks with names such as ```Untitled.ipynb```.

#### 2. Interface Overview

A notebook contains several important interface elements.

**Menu Bar**

The menus provide access to functions such as:

* opening and saving files,
* inserting and deleting cells,
* changing cell types,
* running cells,
* restarting or interrupting the kernel,
* viewing keyboard shortcuts.

**Toolbar**

The toolbar provides quick access to frequently used actions. Depending on your version, these may include:

* Save
* Add Cell
* Run
* Interrupt
* Restart Kernel

**Kernel**

The notebook's kernel executes your Python code. If a computation becomes stuck or you want to clear the current computational state, you can interrupt or restart the kernel from the **Kernel** menu.

**Cells**

Your notebook is constructed from cells.

* **Code cells** contain Python instructions.
* **Markdown cells** contain explanatory text.

### 2.3.1 Add and Run a Code Cell

Click inside a code cell and type:

```python
print("Hello, World!")
```

To run the cell:

* Press ```Shift + Enter```, or
* Click the **Run** button.

You should see:

```text
Hello, World!
```

displayed beneath the cell.

The notebook also records the order in which code cells have been executed. You may see an execution number such as:

```text
[1]
```

next to a cell after it runs.

![Hello, World example](/assets/images/jupyter/tutorial-jupyter-2.6.png)

### 2.3.2 Insert a Markdown Cell

Now add a new cell below your code cell.

Change its type from **Code** to **Markdown** using the cell-type dropdown or menu.

![Dropdown location](/assets/images/jupyter/tutorial-jupyter-2.7.png)

Paste the following into the Markdown cell:

```markdown
### What Just Happened?

We wrote a simple Python command: `print("Hello, World!")`.

- `print(...)` is a Python **function** that displays text.
- `"Hello, World!"` is a **string**, which means it is a sequence of characters or text.
```

Run the Markdown cell with ```Shift + Enter```.

Instead of executing Python, Jupyter will render the Markdown as formatted text.

![Output after running the above instruction](/assets/images/jupyter/tutorial-jupyter-2.8.png)

This ability to alternate between code and explanatory prose is one of the most useful features of notebooks for historians. You can use Markdown cells to explain why you performed a particular operation, identify the sources being analyzed, record methodological decisions, or interpret results.

### 2.4 Save and Shut Down the Notebook

#### 1. Save Your Notebook

Jupyter periodically autosaves notebooks, but you should still save manually before closing your work.

Use:

```text
Ctrl + S
```

on Windows/Linux or:

```text
Cmd + S
```

on macOS.

You can also use the **Save** option in the interface.

Your notebook is saved as an ```.ipynb``` file in the folder where you created it.

> **Important:** Saving the notebook saves its current cells and, normally, the outputs displayed inside them. It does not automatically save external datasets that your code may depend on.

#### 2. Shut Down the Notebook

Closing the browser tab does not necessarily stop the notebook's kernel.

To shut down a notebook completely:

1. Return to the Jupyter Dashboard.
2. Open the **Running** section or tab.
3. Locate the notebook or kernel you want to stop.
4. Select **Shutdown**.

![Shutting Down the Notebook](/assets/images/jupyter/tutorial-jupyter-2.9.png)

If you launched Jupyter from a Terminal or Anaconda Prompt, you can stop the entire Jupyter server by returning to that terminal and pressing:

```text
Ctrl + C
```

Confirm if prompted.

---

## 3. Going Further

Once you are comfortable creating and running notebooks, you can begin using them for more substantial historical research workflows.

### 3.1 Markdown for Research Documentation

Markdown cells can contain headings, lists, links, quotations, images, and other formatted text.

For example:

```markdown
# Ottoman Newspaper Corpus

## Source

These files were digitized from...

## Method

The following cells normalize the text before analysis.

## Observations

- Several spelling variants appear in the corpus.
- The preprocessing step changes...
```

For historians, Markdown cells are not merely decoration. They provide a place to document provenance, methodological decisions, uncertainties, and interpretation alongside computational operations.

### 3.2 Installing and Importing Additional Packages

Python packages provide additional functionality.

Two commonly used packages are:

* **pandas** for working with tabular data.
* **matplotlib** for creating visualizations.

Anaconda Distribution already includes many widely used packages, so they may already be installed.

To test them, create a code cell and run:

```python
import pandas as pd
import matplotlib.pyplot as plt
```

If a package is missing, one option is to install it from a Terminal or Anaconda Prompt using:

```bash
conda install pandas matplotlib
```

Another option, particularly when working inside a notebook, is IPython's ```%pip``` command:

```python
%pip install pandas matplotlib
```

After installing a new package, you may need to restart the kernel before importing it.

> **Why environments matter:** A package must be available to the Python environment used by your notebook's kernel. If you have several Python environments installed, installing a package in one environment does not automatically make it available in another.

### 3.3 JupyterLab and Extensions

Once you are comfortable with notebooks, you may want to try **JupyterLab**. JupyterLab lets you work with multiple notebooks, terminals, text files, data files, and other resources in the same browser workspace.

If you installed Anaconda Distribution, JupyterLab is normally available alongside Jupyter Notebook. You can launch it from Anaconda Navigator or, from an appropriate environment, with:

```bash
jupyter lab
```

Modern versions of Jupyter Notebook use the same general extension system as JupyterLab.

> **Compatibility note:** Older tutorials on the web may instruct you to install ```jupyter_contrib_nbextensions``` or use the old ```jupyter nbextension``` workflow. Those instructions were designed for older versions of the classic Notebook interface and should not be assumed to work with current Jupyter Notebook versions. Check that an extension explicitly supports your current Notebook or JupyterLab version before installing it.

For beginners, extensions are optional. Become comfortable with the standard notebook interface before adding them.

### 3.4 Sharing Notebooks

The original ```.ipynb``` file can be shared with colleagues or students. If they have an appropriate Jupyter environment, they can open the notebook and rerun its code.

GitHub can also display many ```.ipynb``` files directly in the browser, which can be useful for sharing computational workflows without requiring readers to run the notebook themselves.

You can also export notebooks to formats such as **HTML** or **PDF** through Jupyter's File menu. The exact menu label may differ slightly between versions, and PDF export may require additional software.

> **Before sharing:** Check notebook outputs carefully. Outputs can contain data, file paths, identifiers, or other information that you did not intend to publish.

### 3.5 Reproducibility and Historical Research

A notebook is most useful when someone can understand not only **what** you did but also **how** and **with what materials**.

For research notebooks, consider recording:

* the source and provenance of your data,
* what transformations you performed,
* which files served as inputs,
* important software or package versions,
* methodological decisions,
* exclusions or corrections,
* assumptions and uncertainties,
* the meaning of the resulting tables, statistics, or visualizations.

Whenever possible, keep original source data unchanged and perform transformations on copies or derived files.

As projects become more complex, you can also preserve the computational environment using files such as ```environment.yml``` or ```requirements.txt```. These record the software dependencies needed to rerun the analysis.

---

## 4. Keyboard Shortcuts

Keyboard shortcuts can make notebook work considerably faster.

Common shortcuts include:

* **Shift + Enter:** Run the current cell and move to the next.
* **Ctrl + Enter:** Run the current cell and remain on it.
* **Enter:** Enter edit mode for the selected cell.
* **Esc:** Return to command mode.
* **A** in command mode: Insert a cell above.
* **B** in command mode: Insert a cell below.
* **M** in command mode: Change the selected cell to Markdown.
* **Y** in command mode: Change the selected cell to Code.
* **D, D** in command mode: Delete the selected cell.

Keyboard shortcuts can change between Jupyter versions or be customized. If a shortcut does not work as expected, check **Help → Keyboard Shortcuts** or the equivalent shortcut menu in your version.

---

## 5. Troubleshooting & Tips

### 5.1 Jupyter Won't Launch

**Issue:** Running:

```bash
jupyter notebook
```

does not open Jupyter or produces a command-not-found error.

**Possible fixes:**

* On Windows, try launching Jupyter from **Anaconda Navigator** or **Anaconda Prompt** rather than an ordinary Command Prompt.
* Make sure the appropriate conda environment is active:

```bash
conda activate base
```

Then try:

```bash
jupyter notebook
```

* Close and reopen your Terminal or Anaconda Prompt after installing Anaconda.
* If Jupyter Notebook is not installed in the environment you are using, install it with:

```bash
conda install notebook
```

### 5.2 Kernel Is Busy or Not Responding

**Issue:** A cell appears to run indefinitely, or the kernel stops responding.

First try:

**Kernel → Interrupt Kernel**

If that does not work, try:

**Kernel → Restart Kernel**

Restarting the kernel clears the current computational state, so variables and imported packages will need to be recreated by rerunning the relevant cells.

### 5.3 No Kernel or Wrong Kernel

**Issue:** Jupyter cannot find a Python kernel, or your notebook appears to be using the wrong Python environment.

Check which kernel is selected through the notebook's kernel menu.

If you are using a conda environment, activate that environment before launching Jupyter:

```bash
conda activate your-environment-name
```

If necessary, install Jupyter or ```ipykernel``` in that environment.

### 5.4 “Module Not Found” Errors

**Issue:** You run:

```python
import pandas as pd
```

and see something like:

```text
ModuleNotFoundError: No module named 'pandas'
```

The package is not available to the Python environment used by the current kernel.

From inside the notebook, you can try:

```python
%pip install pandas
```

or install it in the relevant conda environment from a Terminal or Anaconda Prompt:

```bash
conda install pandas
```

After installation, restart the kernel and try the import again.

### 5.5 Notebook Is in the Wrong Folder

If your code cannot find a data file, the issue may be the path rather than the code itself.

Run:

```python
import os
print(os.getcwd())
```

This displays the notebook's current working directory.

You can then check whether the file you are trying to open is actually located there or whether you need to provide a relative or absolute path.

### 5.6 Plot Does Not Appear

Modern Jupyter environments normally display ```matplotlib``` plots directly in notebook output.

For example:

```python
import matplotlib.pyplot as plt

plt.plot([1, 2, 3], [2, 4, 3])
plt.show()
```

If no plot appears:

* make sure the cell actually finished running,
* check that ```matplotlib``` imported successfully,
* restart the kernel and rerun the required cells,
* make sure ```plt.show()``` is included.

Older tutorials may tell you that ```%matplotlib inline``` is always required. In modern Jupyter environments, it is generally unnecessary for ordinary notebook plotting.

### 5.7 Accidentally Deleted a Cell

If you delete a cell accidentally, use the notebook's **Undo Cell Operation** command if available.

The default command-mode shortcut ```Z``` may also undo the most recent deleted cell.

### 5.8 Getting Help with Generative AI

Generative AI tools can sometimes help interpret error messages, but they work best when you provide enough context.

A useful debugging prompt should include:

1. Your operating system.
2. Whether you are using Jupyter Notebook or JupyterLab.
3. How you installed Python/Jupyter.
4. The command or code you ran.
5. The complete error message.
6. What you expected to happen.

For example:

> I'm using Jupyter Notebook with Anaconda on macOS. When I run `import pandas as pd`, I get `ModuleNotFoundError: No module named 'pandas'`. How can I check which Python environment my notebook is using and install pandas in the correct environment?

> **Privacy and security:** Before pasting an error message, screenshot, notebook, or code into a generative AI system, check it for passwords, API keys, personal information, restricted research data, unpublished source material, or sensitive file paths. Never share authentication credentials with a chatbot.

---

## 6. Further Resources

### Official Jupyter Documentation

* Project Jupyter: [https://jupyter.org/](https://jupyter.org/){:target="_blank" rel="noopener"}
* Installing Jupyter: [https://jupyter.org/install](https://jupyter.org/install){:target="_blank" rel="noopener"}
* Jupyter Notebook documentation: [https://jupyter-notebook.readthedocs.io/](https://jupyter-notebook.readthedocs.io/){:target="_blank" rel="noopener"}
* JupyterLab documentation: [https://jupyterlab.readthedocs.io/](https://jupyterlab.readthedocs.io/){:target="_blank" rel="noopener"}
* Try Jupyter in your browser: [https://jupyter.org/try](https://jupyter.org/try){:target="_blank" rel="noopener"}

### Anaconda

* Anaconda Distribution: [https://www.anaconda.com/download](https://www.anaconda.com/download){:target="_blank" rel="noopener"}
* Anaconda documentation: [https://www.anaconda.com/docs](https://www.anaconda.com/docs){:target="_blank" rel="noopener"}

### Python Basics

* Official Python Tutorial: [https://docs.python.org/3/tutorial/](https://docs.python.org/3/tutorial/){:target="_blank" rel="noopener"}

### Markdown

* Markdown Guide Cheat Sheet: [https://www.markdownguide.org/cheat-sheet/](https://www.markdownguide.org/cheat-sheet/){:target="_blank" rel="noopener"}

### Data Analysis with Python

* pandas: Getting Started: [https://pandas.pydata.org/docs/getting_started/](https://pandas.pydata.org/docs/getting_started/){:target="_blank" rel="noopener"}
* Matplotlib tutorials: [https://matplotlib.org/stable/tutorials/](https://matplotlib.org/stable/tutorials/){:target="_blank" rel="noopener"}

### Help and Community

* Stack Overflow: Search for the exact Jupyter or Python error message you are encountering.
* Jupyter Community Forum: [https://discourse.jupyter.org/](https://discourse.jupyter.org/){:target="_blank" rel="noopener"}
