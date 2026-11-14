\# Comprehensive Git \& GitHub Reference Guide (STS / Eclipse)



A complete, single-file reference covering terminal path verification, GitHub prerequisites, and all Git use cases (brand-new projects, repository migrations, history retention, troubleshooting, and daily workflows) with detailed explanations.



\---



\## Step 1: GitHub Setup \& Authentication Prerequisites



Before running any commands in your local terminal, you need an empty destination repository on GitHub and a secure authentication token so GitHub accepts pushes from your machine.



1\. \*\*Create an Empty Repository:\*\* 

&#x20;  \* On GitHub, click \*\*+\*\* (top-right) -> \*\*New repository\*\*.

&#x20;  \* Give it a repository name (e.g., `jwt-demo`).

&#x20;  \* \*\*Important:\*\* Do \*\*NOT\*\* check \*"Add a README file"\*, \*"Add .gitignore"\*, or \*"Choose a license"\*. Initializing a repository with files on GitHub creates an initial remote commit that conflicts with your existing local project files during your first push.

&#x20;  \* Click \*\*Create repository\*\* and copy the \*\*HTTPS URL\*\* (e.g., `https://github.com/your-username/your-repo.git`).



2\. \*\*Generate a Personal Access Token (PAT):\*\* 

&#x20;  \* GitHub no longer accepts standard account passwords for command-line Git operations. You must use a Personal Access Token instead.

&#x20;  \* Go to GitHub \*\*Settings\*\* -> \*\*Developer settings\*\* -> \*\*Personal access tokens\*\* -> \*\*Tokens (classic)\*\*.

&#x20;  \* Click \*\*Generate new token (classic)\*\*, check the \*\*`repo`\*\* scope (which grants full read/write access to your repositories), and generate it.

&#x20;  \* Copy the token (`ghp\_...`) and save it somewhere safe—GitHub only displays it once.



\---



\## Step 2: Verify Terminal Path (Crucial Check)



In STS or Eclipse, right-click your project folder -> \*\*Show In -> Terminal\*\*. 



Before typing any Git commands, verify you are inside the \*\*root project folder\*\* (where `pom.xml` and `src/` live), \*\*NOT\*\* your parent Eclipse workspace folder. Running Git commands in the parent workspace will accidentally track every unrelated project in your workspace.



```cmd

dir

```



\* \*\*Correct Path:\*\* You see `pom.xml` and `src` listed directly.

\* \*\*Incorrect Path (Parent Workspace):\*\* You see a list of multiple project folders. Run `cd <your-project-folder>` to step inside your specific project first.



\---



\## Step 3: Use Case Scenarios (Choose Your Situation)



\### Use Case A: Brand-New Project (Never Initialized with Git)

Use this workflow when your project folder has never been tracked by Git and does not contain a hidden `.git` folder yet.



\* \*\*`git init`\*\*: Creates a hidden `.git` subfolder that initializes version control tracking for this directory.

\* \*\*`git add .`\*\*: Scans your project and stages all new and modified files for commit (automatically ignoring build folders like `/target` if a `.gitignore` file is present).

\* \*\*`git commit -m "..."`\*\*: Saves a permanent local snapshot of your staged files with a descriptive message.

\* \*\*`git branch -M main`\*\*: Renames the default local branch to `main` to match GitHub's modern default branch standard.

\* \*\*`git remote add origin <url>`\*\*: Links your local repository to your remote GitHub repository under the shortcut name `origin`.

\* \*\*`git push -u origin main`\*\*: Uploads your local `main` branch commits to GitHub and sets `origin/main` as the upstream tracking branch so future pushes only require `git push`.



```cmd

git init

git add .

git commit -m "Initial commit"

git branch -M main

git remote add origin \[https://github.com/your-username/your-repo.git](https://github.com/your-username/your-repo.git)

git push -u origin main

```



\### Use Case B: Migrating an Existing Project to a Brand-New GitHub Repo (Clean History)

Use this workflow when your project already has an old `.git` folder (from a previous repository or Spring Initializr) and you want to \*\*completely wipe the old commit history and remote links\*\* to start fresh on a brand-new repository.



\* \*\*`rmdir /s /q .git`\*\*: Deletes the hidden `.git` directory, completely removing all previous commit history, branches, and old remote repository connections while leaving your actual source code untouched.

\* \*\*Re-initialization (`git init` -> `git push`)\*\*: Treats the project as a brand-new codebase and pushes a single, clean initial commit to your new GitHub repository.



```cmd

\# 1. Remove old .git folder from your project root

\# Windows CMD:

rmdir /s /q .git

\# (Windows PowerShell: Remove-Item -Recurse -Force .git)

\# (Git Bash / Mac / Linux: rm -rf .git)



\# 2. Initialize a fresh local repository

git init



\# 3. Stage and commit all files cleanly

git add .

git commit -m "Initial commit: Fresh repository setup"



\# 4. Set branch to main and link your new GitHub repository

git branch -M main

git remote add origin \[https://github.com/your-username/your-new-repo.git](https://github.com/your-username/your-new-repo.git)



\# 5. Push to GitHub

git push -u origin main

```



\### Use Case C: Switching to a New Repo While Keeping Existing Commit History

Use this workflow when you already committed code locally, want to \*\*preserve your existing commit history\*\*, and simply need to point the project to a different GitHub repository URL.



\* \*\*`git remote set-url origin <url>`\*\*: Overwrites the existing `origin` remote URL in your `.git/config` file with your new repository URL without touching your local commits.



```cmd

git remote set-url origin \[https://github.com/your-username/your-new-repo.git](https://github.com/your-username/your-new-repo.git)

git push -u origin main

```



\---



\## Step 4: Troubleshooting Common Git Errors \& Edge Cases



\### 1. Accidental `git init` in Parent Workspace Folder

\* \*\*Root Cause:\*\* You ran `git init` one directory level too high (inside your Eclipse workspace folder instead of your project folder), causing Git submodule warnings (`git rm --cached`) or attempting to track unrelated workspace projects.

\* \*\*Exact Fix:\*\* Step up to the parent folder, delete the accidental `.git` folder, and step back into your actual project folder:

&#x20; ```cmd

&#x20; cd ..

&#x20; rmdir /s /q .git

&#x20; cd <your-project-folder>

&#x20; ```



\### 2. `Authentication failed` / Old Credentials Cached

\* \*\*Root Cause:\*\* Windows Credential Manager or Git's credential cache automatically submits an expired token or old password without prompting you to enter your new Personal Access Token.

\* \*\*Exact Fix:\*\* Purge the saved GitHub credentials from Git's credential manager so it prompts you fresh:

&#x20; 1. Run `git credential-manager erase`

&#x20; 2. Type `protocol=https` and press \*\*Enter\*\*.

&#x20; 3. Type `host=github.com` and press \*\*Enter twice\*\* (the blank line signals the end of input and returns your terminal prompt).

&#x20; 4. Run `git push -u origin main` again and enter your GitHub username + PAT.



\### 3. Bypass Credential Manager Immediately

\* \*\*Root Cause:\*\* Your terminal refuses to prompt for your new username and PAT due to stored system credential helpers.

\* \*\*Exact Fix:\*\* Run this single-line command to temporarily disable credential helpers for one push and force an immediate username/PAT prompt:

&#x20; ```cmd

&#x20; git -c credential.helper= push -u origin main

&#x20; ```



\### 4. `Author identity unknown` Error on Commit

\* \*\*Root Cause:\*\* Git requires a name and email address to attach to every commit snapshot, and global user details have not been configured on your machine yet.

\* \*\*Exact Fix:\*\* Configure your Git identity once globally:

&#x20; ```cmd

&#x20; git config --global user.name "Your Name"

&#x20; git config --global user.email "you@example.com"

&#x20; ```



\### 5. `LF will be replaced by CRLF` Warning

\* \*\*Root Cause:\*\* Windows uses Carriage Return + Line Feed (`CRLF`) for line endings, whereas Linux/macOS and Git repositories standardize on Line Feed (`LF`). Git automatically converts them during staging.

\* \*\*Exact Fix:\*\* \*\*No action needed.\*\* This is an informational notice, not an error—it will not break your Java code or cloud builds.



\---



\## Step 5: Pushing Subsequent Local Changes Later (Daily Workflow)



Once your project is connected to GitHub, Git remembers your repository URL (`origin`) and default branch (`main`). Whenever you edit Java files, add new endpoints, or update `application.properties` in STS later on, you do \*\*not\*\* run `git init`, `git branch`, or `git remote add` again. Instead, repeat this four-step cycle inside your project root folder:



1\. \*\*Check Modified Files (Optional):\*\* Lists all files you created, modified, or deleted since your last commit so you can verify what is being uploaded.

&#x20;  ```cmd

&#x20;  git status

&#x20;  ```

2\. \*\*Stage Updated \& New Files:\*\* Collects all your local changes into Git's staging area to prepare them for the next snapshot.

&#x20;  ```cmd

&#x20;  git add .

&#x20;  ```

3\. \*\*Commit the Changes Locally:\*\* Records a new version checkpoint on your local machine with a clear summary of what changed.

&#x20;  ```cmd

&#x20;  git commit -m "Describe what you updated or added"

&#x20;  ```

4\. \*\*Push the New Commit to GitHub:\*\* Uploads your local commit(s) to your `main` branch on GitHub. \*(If auto-deploy is enabled on Koyeb or Render, pushing to `main` also triggers a fresh cloud deployment automatically).\*

&#x20;  ```cmd

&#x20;  git push

&#x20;  ```



> \*\*Tip:\*\* If you ever edited a file directly on GitHub's website (such as `README.md`), run `git pull` before `git push` so your local machine downloads and merges those remote changes first.

