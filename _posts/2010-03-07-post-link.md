---
title: "GitHub for Historians: Building and Publishing with GitHub Pages"
categories:
  - Beginner
tags:
  - link
  - Post Formats
# link: https://github.com
toc: true
---

A hands‑on, beginner‑friendly guide to creating and publishing a personal or project website by cloning an existing template on GitHub, editing content in Markdown, and using GitHub Desktop to track and publish your work. No command‑line or coding experience required.

---

**Need help along the way? Use ChatGPT or any other Generative AI!**

If you get stuck, don’t worry—many issues are easy to fix. Try asking ChatGPT for help. Just copy-paste the error message you see on GitHub (or take a screenshot of what you’re seeing), and ask something like:

*“I’m following a tutorial to publish a site using GitHub Pages, and I’m getting this error. How can I fix it?”*

You can also ask general questions like:

*“What does this file do?” or “How do I find my Markdown files?”*

---

## 1. Why GitHub for Historians?

GitHub is more than a programmers’ playground: it’s a free, version‑controlled, and openly accessible platform for storing any text‑based project. For historians, that means:
Keeping a transparent record of revisions to research notes, teaching materials, or digital exhibits.
Collaborating smoothly with co‑authors or students.
Publishing a professional website at your‑username.github.io with minimal setup.

---

## 2. Prerequisites

| What | Why |
| ----------- | ----------- |
| GitHub account | Creates a personal space for your repository & website. |
| GitHub Desktop (Mac/Windows) | A graphical app that replaces command‑line Git for commits & pushes. |
| A ready‑made template on GitHub (e.g. Jekyll) | Saves time—most styling & navigation are done for you. |
| Text editor (VS Code, Sublime, Atom, etc.) | Edits Markdown files locally. |

> **Tip for classrooms:** Students can sign up with an academic email and request the free [GitHub Education](https://education.github.com/pack) benefits bundle, which includes access to a wide range of developer tools and resources.

## 3.  Setting up GitHub Account, GitHub Desktop  & Visual Studio Code

### Step 1. Sign Up for GitHub
1. Navigate to [github.com](https://github.com/) → Sign up.
2. Choose a username (this will be visible in your site URL later).
3. Verify your email & complete the short survey.

### Step 2. Download GitHub Desktop
1. Go to [desktop.github.com](https://desktop.github.com/) and download for your OS.
2. Install and sign in with the account you just created.

### Step 3. Download MS Visual Studio Code
1. Go to [code.visualstudio.com/download](https://code.visualstudio.com/download) and download it for your OS via the .zip method. The .zip install is good for beginners, while advanced users can use other installation methods like the command line.
2. Open the downloaded zip file and follow the instructions.

![GitHub “Sign up” page](/assets/images/tutorial-github-3.1.png)

![GitHub Desktop first‑run window](/assets/images/tutorial-github-3.2.png)


---
## 4. Set Up Your Website Repository from a Template

**What is a repository?** A repository (or repo) is a container or folder for your project. It holds all the files, images, and code your site needs.

**How is a website linked to a repository?** GitHub Pages automatically turns the contents of a repository named your-username.github.io into a live website at that exact URL. The name must match your GitHub username exactly.

GitHub allows one website repository per user at the your-username.github.io address. You can still make unlimited project sites under different names, but for your homepage, only one repo can be tied to that web address.

**What to do if I already have a repository named your-username.github.io?** If you already have a repository named your-username.github.io that has a corresponding website published via GitHub Pages, what you can do is:

**Option 1: Delete the Existing Repository**
* If you want to start fresh, you can delete the current repository and create a new one using a different template.
* Follow the deletion instructions in this tutorial.
* Once deleted, GitHub will release the web address, and you can reuse ```your-username.github.io```.

**Option 2: Create a Project Site Instead**
* You can only have one personal site at ```your-username.github.io```, but you can still create multiple project sites.
* Just name the new repository anything else (e.g., digital-archive-project) and enable GitHub Pages from its settings.
* Your project site will be published at: ```https://your-username.github.io/digital-archive-project/```

### Step 1. Choose a Template Repository
1. In a browser, open the template repository you want to use. For example, one of the templates you can use is the  Academic Pages template available at https://github.com/academicpages/academicpages.github.io .
2. Confirm it displays a green Use this template button (templates are specially flagged by their authors).

!["Use this template" button](/assets/images/tutorial-github-4.1.png)

You can look for more Jekyll-based templates here:
* [https://github.com/topics/jekyll-theme](https://github.com/topics/jekyll-theme)
* [https://jamstackthemes.dev/ssg/jekyll/](https://jamstackthemes.dev/ssg/jekyll/)
* [https://jekyll-themes.com/](https://jekyll-themes.com/)

### Step 2. Generate Your New Repository
1. On the template repository page, click **Use this template → Create a new repository**.
2. Name it ```your‑username.github.io``` (replace *your‑username* with your GitHub handle). This naming convention signals GitHub Pages to publish to that URL automatically.
3. Keep the repo **Public** (free Pages sites require public visibility).
4. Click **Create repository** from template. This would copy all the code and files provided by the template into your repository.

### Step 3. Clone the Repository Locally with GitHub Desktop
1. In the success screen, click on **Code**, then click **Open with GitHub Desktop**. Approve the browser prompt.
2. Choose a local folder where you’ll keep website files.
3. Click **Clone**.

![GitHub Desktop “Clone a repository” dialog](/assets/images/tutorial-github-4.2.png)

---

## 5. Tour Your New Repository & Folder Structure

Once you've cloned your repository with GitHub Desktop, all the files that make up your website will be saved to your computer.

### How to Find Your Repository on Your Computer
* Open GitHub Desktop.
* In the top menu, go to **Repository → Show in Finder** (Mac) or **Show in Explorer** (Windows). This opens the exact folder where your website files are saved.
* You can also search your computer for the folder name (usually the name of your repository, like ```your-username.github.io```).

### Typical Folder Structure

While every template is slightly different, most **Jekyll‑based templates** follow a familiar structure. After cloning your chosen template, you’ll typically see in your local repository a structure similar to:

─ _config.yml            ← Global settings for your site (title, author, etc.)
─ _posts/                ← Blog posts, each in its own .md file
─ pages/ or _pages/      ← Main site pages (About, CV, etc.)
─ assets/ or images/     ← Media like images and PDFs
─ index.md or index.html ← Homepage content
─ README.md              ← Notes about your project (not part of website)

### Which Folders To Explore

Ignore these folders for now:
* ```_layouts/, _includes/, _sass/``` → These affect design and structure but don’t need to be edited for simple text changes.

If your goal is just to update your website text and content, you’ll only be editing Markdown files found inside folders such as:
* ```_pages/ or pages/``` → Contains main content like About, CV, etc.
* ```_posts/``` → Contains blog posts (optional depending on your template)
* Other folders like ```_publications```, etc., depending on your template

You might need to work with folders like ```assets/``` or ```images/``` as well, as this is where images or uploaded files go.
Don’t worry if the folders look slightly different depending on your template. Focus only on the folders containing .md files and you’ll be fine!

![Folders containing Markdown files or image/pdfs when working with the Academic Pages template](/assets/images/tutorial-github-5.1.png)

---

## 6. Publish with GitHub Pages

Before editing anything, it’s helpful to publish the website once and see what it looks like. This will help you understand which parts of the site map correspond to which files.

### Step 1. Enable Pages (Only Once)
1. Go to your repository on **GitHub.com → Settings → Pages**.
2. Under Build and deployment, choose Deploy from a branch.
3. For **Branch**, pick ```main``` and ```/ (root)``` folder. Click **Save**.

> **Note:** If the **Save** button is inactive or if GitHub Pages is already enabled for a branch, it means your site is likely already configured to deploy from that branch. You can verify this by checking the "GitHub Pages" section for a live site link.

Wait 1–2 minutes. A green banner will show saying your site will be live at https://your‑username.github.io/. 

Every time you **push** new commits, GitHub automatically rebuilds and publishes the site.


#### If the button to Publish doesn’t appear

Many templates, including the **Academic Pages** template we’re using, use a tool called **GitHub Actions** to build your website and publish it to a special branch called ```gh-pages```. But by default, GitHub doesn’t give this tool permission to make changes — so the build fails and the website never appears.

**Step-by-Step Fix**
1. Give GitHub Actions Permission to Publish
* On your forked repository page, click Settings (top of the page)
* In the left menu, click Actions → General
* Scroll down to the section called Workflow permissions
* Select ```Read and write permissions```. Also check the box that says **“Allow GitHub Actions to create and approve pull requests”** (if you see it)
* Click **Save**


2. Trigger the Build
Now that permissions are fixed, you need to trigger the **GitHub Actions** tool to start building the site. Do one of these:
* Easiest: Go to the repo home page, open any file (like ```README.md```), click the pencil ✏️ to edit, make a tiny change (like adding a space), and click Commit changes.
* OR: Go to the Actions tab → click on the Deploy workflow → click the “Run workflow” button.


3. Wait for the Build to Finish
* After 1–2 minutes, go to the **Actions tab**
* You should see a green ✔️ next to **Deploy**
* After a few seconds, your site will be live and you can get the link by navigating to **Settings → Pages**


### Step 2. Compare Website to Markdown Files
* Open your published site in a browser on the link https://your‑username.github.io/.
* Click on a page like “About” or “Home.”
* Go back to your local folder on your computer and find the matching ```.md``` file (for example, ```about.md``` or ```index.md```).
* Open that file in your editor and compare the visible text on the site to the text in the file.

---

## 7. Editing Website Content

Jekyll-based websites use **Markdown** (```.md``` files) for writing content. If you are just planning to change the content of the website (such as text, images), you only need to edit the text in these markdown files (no need to edit other files).

Each Markdown file has two parts:
1. **Front matter** (between ```---``` lines at the top)
2. **Body text** (your actual content)

### Example of a Markdown File

Here’s an example of an ```about.md file``` from the **Academic Pages** template used: 


![about.md file from the **Academic Pages** template](/assets/images/tutorial-github-7.1.png)

Here, the **front-matter** section is:

```ruby
permalink: /
Title: “Academic Pages is a ready-to-fork GitHub Pages template for academic personal websites”
author_profile: true
Redirect_form:
/about/
/about.html
```

### Which Parts Can You Edit?

In the **front matter**, try to only change simple fields like ```title:```.
* **Title:** controls the title that appears at the top of the page or in the tab of your browser (depending on the template)

In the **body text**, you can freely write or delete content using basic formatting.

For example, the body section of the ```about.md``` file starts with: *This is the front page of the website that is powered by the [Academic Pages Template]...*

There are two ways to insert headings in the body section:
1. **Underlined style** (limited to only 2 types of headings): The line of = creates an H1 (Heading level 1); the line of - creates an H2 (Heading level 2).

```ruby
Heading Level 1
===============
Heading Level 2
--------------
```

2. **Hash style:** One # makes H1 , two ## makes H2, three ### makes H3, and so on up to H6.

```ruby
# Heading 1
## Heading 2
### Heading 3
```

Either approach works—use **underlines** if you only need H1/H2, or **#** if you want more heading levels (H3–H6) or prefer seeing the # on each line. In the Academic Pages template, most .md files use underline headings by default but you can easly replace them with hash style version.

### Recommended Files for Editing
* ```index.md``` (if available)→ homepage
* ```about.md``` or a file in ```_pages/``` → About page
* Files in ```_posts/``` → blog posts, listed by date


## How to Edit an Existing Page

In most **Jekyll themes**, you can edit the website content by editing corresponding ```.md ```(Markdown) files and see your change on the live site. However, with the **Academic Pages** template used in this tutorial, you also need to update ```url``` and ```baseurl``` in ```_config.yml``` as these are set to point to the original theme author’s site or break links. 

To update them to point to your website’s URL take the following steps:
1. Open your local _config.yml file and make sure these two updates these lines to match your repository’s address:
* ```url:``` Update it with "https://your-username.github.io" or if you already have some other page hosted on Github then use "https://your-username.github.io/[Your Repo Name]"
* ```Repository:``` Update it with your-username.github.io
2. Save changes to ```_config.yml``` file 

Now that our URLs are configured correctly, you can edit contents of ```.md``` files. To do that:
1. Open a file you want to edit (such as ```about.md```) in VS Code (or in your choice of text editor). 
2. Make an edit to the text, i.e. adjust the **title** or the **body** text.
3. Save the file and go back to GitHub Desktop — you’ll see the file marked as “modified.”

For a cheat sheet of Markdown formatting, see: [https://www.markdownguide.org/cheat-sheet/](https://www.markdownguide.org/cheat-sheet/)

Now that you know how to safely find and edit your site’s content, let’s learn how to track and publish changes.


---

## 8. Tracking Changes with Git & GitHub Desktop

**What is Git?**

Git takes snapshots (commits) of your project over time, so you can revert mistakes and see who changed what.

### Step 1. Commit Your Edits

1. In **GitHub Desktop**, the left sidebar lists changed files.
2. Review the difference. The part in red shows the old version of your code, the part in green - the new version. 
3. Tick/untick files you want in this commit.
4. Add a short **Description**: *Edited about page text*.
5. Click **Commit to main**.


![How Changes are displayed on Github Desktop](/assets/images/tutorial-github-8.1.png)


### Step 2. Push (Publish) to GitHub.com

After committing, click **Push origin** (top toolbar). Your commits travel to the online copy of the repo.

![Push origin button](/assets/images/tutorial-github-8.1.png)

### Step 3. Check If Changes Have Been Reflected on the Website

* Navigate to your **GitHub repository** and reload the page.
* Your latest commit should be displayed under the Code button. 
* If an orange dot is displayed next to your commit, it means the changes you requested are still *Pending* (screenshot attached). In this case, you need to wait for a few seconds and refresh it until you get a green tick next to your commit (screenshot attached), which signifies a successful build.
* Open your published site in a browser on the link https://your‑username.github.io/. It might take a few minutes until the changes are reflected on the website as well.

Pending Commit:
![Screenshot: Pending Commit](/assets/images/tutorial-github-8.3-pending.png)

Successful Commit:
![Screenshot: Successful Commit](/assets/images/tutorial-github-8.3-success.png)

Note:
If there’s a red X mark displayed next to your commit, it means there was a build error. Ideally, if you only modify Markdown files, you shouldn’t face this problem, but if you still get this build error, you can try to resolve it using Generative AI such as ChatGPT by pasting your error message (which you can obtain by clicking on that commit and nvaigating to the error message)

### Step 4. View History or Revert
* Use the **History** tab in GitHub Desktop to browse old commits.
* Right‑click a file → **Discard changes** to undo **before committing**. After committing, changes are saved — but you can undo the most recent commit as long as it hasn't been pushed online yet.


## 9. How to Create a New Blog Post
1. Navigate to ```_posts/``` folder in your directory.
2. Create a new Markdown file by clicking **File → New Text file**. 
You can create a file called, for example: ```2025-06-02-blog-post-sample.md``` (If you’re on macOS/Windows: **right-click → New Text Document**, then **rename**.)
3. Add the required front matter at the very top of that file 
Your **front matter** should follow the same format as in the other markdown files for posts. Make sure to edit ```date``` and ```permalink``` fields according to the current date.

```ruby
title: "Sample Post"
date: 2025-06-02
permalink: /posts/2025/06/sample-post/
tags:
 - cool posts
 - category1
 - category2
```
4. Write your post content below the front matter, for example: *In this post, I reflect on my experience integrating GitHub into historical teaching..*
5. Save, then commit & push
6. Check your new post online


### How Different Templates Might Vary

* Nearly all **Jekyll** blog templates use a ```_posts/``` folder and expect filenames like ```YYYY-MM-DD-title.md```.
* Most front matter fields (```title:, date:, tags:```) are very similar across themes—but some may use ```categories:``` instead of ```tags:``` or require additional front-matter keys.

If you switch to another Jekyll theme later, just confirm which front-matter fields the new theme requires (often documented in that theme’s ```README```). But the basic steps—create a date-named .md file in ```_posts/``` with ```layout: post``` and your ```title:/date:```—are almost always the same.

---

## 10. Collaborating with Others

Even without a command line, you can:

| Feature | How‑To in GitHub Desktop |
| ----------- | ----------- |
| **Branch** (safe sandbox) | Menu → Branch → **New branch** → name it ```draft-exhibit```. Switch back via Branch menu. |
| **Pull Request** (propose merge)| On GitHub.com, click Compare & pull request. Write a message describing changes. |
| **Review** | Comment or suggest edits in the PR “Files changed” tab. |

---

## 11. Deleting Your Repository

If you no longer want to keep your website live or want to start over, you can delete your GitHub repository. Here’s how:
1. Go to github.com and navigate to your repository page.
2. Click **Settings** in the top menu (you may need to scroll to the right).
3. Scroll to the bottom of the page to **Danger Zone**.
4. Click **Delete this repository**.
5. You’ll be asked to type the repository name to confirm, then click **I understand the consequences, delete this repository**.

> **Warning:** This will permanently delete all files and disable the website at your GitHub Pages address.

---

## 12. Troubleshooting & Tips

| Issue | Likely Cause | Fix |
| ----------- | ----------- | ----------- |
| Page returns **404 Not Found** | GitHub Pages hasn’t finished building yet | Wait 1–2 minutes and refresh the page. You can also check the **Actions** tab in your repository for build errors. |
| Images not showing | File path is incorrect or image isn’t in the ```assets/``` or ```images/``` folder | Double-check that the image file name matches exactly (watch for .jpg vs .JPG). Use lowercase letters and avoid spaces. |
| Changes not appearing on website | You forgot to **push** your changes | You forgot to push your changes |
| Website layout looks broken | You may have accidentally changed a layout or configuration file | Avoid editing files in ```_layouts/, _includes/```, or ```_sass/``` unless you know what they do. Restore from history if needed. |
| “Your site is having problems building” error on GitHub | There may be a mistake in your Markdown front matter (e.g., missing a quote or colon) | Go to the **Actions** tab on GitHub to see the specific error message. Fix the formatting and push again. |
| Can't find where to edit page content | You're looking at the wrong file or the content is generated from a template | Start by editing Markdown files like ```index.md, about.md```, or files inside ```_pages/```. Preview the live site to match what file maps to what page. |
| Accidentally deleted a file | File is missing from the repository | In GitHub Desktop, go to the **History** tab, right-click the last good commit, and revert or copy the file contents from there. |

---

## 12. Further Resources

* GitHub Docs – Pages: [https://docs.github.com/pages](https://docs.github.com/pages)
* GitHub Desktop Guides: [https://docs.github.com/desktop](https://docs.github.com/desktop)
* Markdown Guide: [https://www.markdownguide.org/cheat-sheet/](https://www.markdownguide.org/cheat-sheet/)
* Minimal Mistakes theme: [https://mmistakes.github.io/minimal-mistakes/](https://mmistakes.github.io/minimal-mistakes/) (live demo & docs)