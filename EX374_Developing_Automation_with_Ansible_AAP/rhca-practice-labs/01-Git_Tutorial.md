# Git 101 Tutorial

This guide provides comprehensive instructions for installing, configuring, and using Git on **Windows** and **RHEL 9** hosts. It also explains the Git workflow with a detailed description of file states and commands.

---

## **1. What is Git?**
Git is a distributed version control system (DVCS) that enables you to manage changes to files in a project collaboratively. Using Git provides many benefits:

- You can review and restore earlier versions of files.
- You can compare two versions of the same file to identify changes.
- You can record a log of who made what changes, and when those changes were made.
- Multiple users can collaboratively modify files, resolve conflicting changes, and merge the changes.

Using Git, you can start by cloning an existing shared project from a remote repository. Cloning a project creates a complete copy of the original remote repository as a local repository. This local copy has the entire history of the files in Git, not just the latest snapshot of the project files.

---

## **2. Installing Git**

### **For Windows Hosts:**
1. Download the Git installer from [git-scm.com](https://git-scm.com/).
2. Run the installer and follow these steps:
   - Select default settings unless you need custom configurations.
   - Ensure "Git Bash" and "Git GUI" are installed.
   - Choose the recommended editor (default is Vim).
   - Configure PATH to "Use Git from the command line and also from 3rd-party software."
3. Verify installation:
   ```bash
   git --version
   ```

### **For RHEL 9 Hosts:**
1. Open a terminal.
2. Update the system packages:
   ```bash
   sudo dnf update -y
   ```
3. Install Git:
   ```bash
   sudo dnf install git -y
   ```
4. Verify installation:
   ```bash
   git --version
   ```

---
[Click Here to Access the Cheat Sheet at the Bottom of the Page](https://github.com/RedHatRanger/RHCA9Vagrant/blob/main/EX374_Developing_Automation_with_Ansible_AAP/rhca-practice-labs/01-Git_Tutorial.md#git-cheat-sheet)
---

## **3. Configuring Git**

After installation, set up your user identity for Git commits.

### **Commands for Both Windows and RHEL 9:**
```bash
git config --global user.name "Your Name"
git config --global user.email "your_email@example.com"
```

Verify the configuration:
```bash
git config --list
```

To enhance your Bash shell prompt to show Git status (optional):
1. Add the following lines to your `~/.bashrc` file:
   ```bash
   source /usr/share/git-core/contrib/completion/git-prompt.sh
   export GIT_PS1_SHOWDIRTYSTATE=true
   export GIT_PS1_SHOWUNTRACKEDFILES=true
   export PS1='[\u@\h \W$(declare -F __git_ps1 &>/dev/null && __git_ps1 " (%s)")]\$ '
   ```
2. Reload the Bash configuration:
   ```bash
   source ~/.bashrc
   ```
---

## **4. Git Workflow Overview**

### **File States in Git**
1. **Modified**: You edited the copy of the file in the working tree, and it is different from the latest version in the repository.
2. **Staged**: You added the modified file to the staging area, but the changes are not yet committed.
3. **Committed**: You saved the changes to the local repository.

Each file transitions through these states during the Git workflow:
- **Edit** → **Stage** → **Commit** → **Push**.

### **Basic Git Commands**

#### **Step 1: Clone a Repository**
Cloning creates a local copy of a remote repository:
```bash
git clone <repository-URL>
```

#### **Step 2: Edit Files**
Use your preferred editor to modify files. Examples:
- Windows: Use Notepad, VS Code, or Git Bash (`vim` or `nano`).
- RHEL 9: Use `vim`, `nano`, or any preferred editor.

#### **Step 3: Stage Changes**
Add files to the staging area:
```bash
git add <file>
```
Add all files:
```bash
git add .
```

#### **Step 4: Commit Changes**
Save changes to the local repository with a message:
```bash
git commit -m "Your commit message"
```

#### **Step 5: Push Changes**
Upload changes to the remote repository:
```bash
git push
```

#### **Step 6: Pull Changes**
Fetch and merge updates from the remote repository:
```bash
git pull
```

---

## **5. Advanced Git Usage**

### **Branching and Merging**

#### **Create a Branch:**
```bash
git branch feature-branch
git checkout feature-branch
```
Or in one step:
```bash
git checkout -b feature-branch
```

#### **Merge a Branch:**
Switch to the main branch and merge:
```bash
git checkout main
git merge feature-branch
```

#### **Delete a Branch:**
```bash
git branch -d feature-branch
```

### **Undoing Changes**

#### **Undo Changes Before Staging:**
```bash
git checkout -- <file>
```

#### **Unstage Changes:**
```bash
git reset <file>
```

#### **Revert a Commit:**
```bash
git revert <commit-hash>
```

---

## **6. Git Best Practices**

### **Meaningful Commit Messages**
- The first line should summarize the commit in under 50 characters.
- Add a blank line and include more details if necessary.
- Reference relevant issues or tickets.

### **Avoid Committing Large Files**
- Use `.gitignore` to exclude unnecessary files.
- Example `.gitignore`:
   ```
   *.log
   node_modules/
   .env
   ```

### **Regularly Pull Updates**
Keep your local repository synced with the remote repository to avoid conflicts:
```bash
git pull
```

---

## **7. Useful Git Commands**

| Command                            | Description                                    |
|------------------------------------|------------------------------------------------|
| `git log`                          | View commit history                           |
| `git status`                       | Check the current status of the repo          |
| `git diff`                         | Show file changes not yet staged              |
| `git clone <repository-URL>`       | Clone a repository to your machine            |
| `git pull origin main`             | Pull the latest changes from GitHub           |
| `git push origin main`             | Push changes to GitHub                        |
| `git revert <commit-hash>`         | Undo changes in a specific commit             |

---

## **8. Tips for Hosts**

### **For RHEL 9:**
- Use `screen` or `tmux` to keep sessions alive.
- Install additional tools:
   ```bash
   sudo dnf install vim-enhanced -y
   ```
- Set up SSH keys for authentication:
   ```bash
   ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
   ```

### **For Windows:**
- Use **Git Bash** for a Unix-like experience.
- Consider Visual Studio Code for its built-in Git integration.
- Set up SSH keys:
   ```bash
   ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
   eval $(ssh-agent -s)
   ssh-add ~/.ssh/id_rsa
   ```

---

## Git Cheat Sheet
![image](https://github.com/user-attachments/assets/1dad6084-b047-431f-8f64-291c2da4780e)

(Image subject to @Copyright by: https://in.linkedin.com/school/tutort/?trk=public_post-text)


<br><br>
And this one...
![image](https://github.com/user-attachments/assets/1ef44904-5c8d-4505-a2f2-6a9c9de1e59f)

(Image subject to @Copyright by: Danny Adams https://dev.to/doabledanny/git-cheat-sheet-50-commands-free-pdf-and-poster-4gcn/comments)

