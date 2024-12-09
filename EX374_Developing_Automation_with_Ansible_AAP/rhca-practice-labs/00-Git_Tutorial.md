# Git Tutorial for Windows and RHEL 9 Hosts

This guide provides step-by-step instructions for installing, configuring, and using Git on **Windows** and **RHEL 9** hosts.

---

## 1. **What is Git?**
Git is a distributed version control system that helps developers track, manage, and collaborate on projects efficiently.

---

## 2. **Installing Git**

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

## 3. **Configuring Git**

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

---

## 4. **Creating a Git Repository**

A Git repository is where Git tracks your project files and history.

1. **Create a directory:**
   ```bash
   mkdir my_project
   cd my_project
   ```
2. **Initialize the repository:**
   ```bash
   git init
   ```
   This creates a `.git` folder where Git stores your project history.

---

## 5. **Basic Git Workflow**

### **Step 1: Edit Files**
Use any editor to create or modify files. Examples:
- Windows: Use Notepad, VS Code, or Git Bash (`vim` or `nano`).
- RHEL 9: Use `vim`, `nano`, or any preferred editor.

### **Step 2: Stage Changes**
Add files to the staging area:
```bash
git add <file>
```
To add all files:
```bash
git add .
```

### **Step 3: Review Changes**
Check the status of your repo:
```bash
git status
```
View differences:
```bash
git diff
```

### **Step 4: Commit Changes**
Save changes to the repository with a message:
```bash
git commit -m "Your commit message"
```

---

## 6. **Connecting to GitHub**

### **Step 1: Create a GitHub Repository**
1. Go to [GitHub](https://github.com/).
2. Create a new repository.
3. Copy the repository URL (HTTPS/SSH).

### **Step 2: Link Local Repo to GitHub**
Add the GitHub remote:
```bash
git remote add origin <repository-URL>
```

### **Step 3: Push Changes**
Upload changes to GitHub:
```bash
git push -u origin main
```

---

## 7. **Branching and Merging**

### **Create a Branch:**
```bash
git branch feature-branch
git checkout feature-branch
```
Or create and switch in one command:
```bash
git checkout -b feature-branch
```

### **Merge a Branch:**
1. Switch to the main branch:
   ```bash
   git checkout main
   ```
2. Merge:
   ```bash
   git merge feature-branch
   ```

### **Delete a Branch:**
```bash
git branch -d feature-branch
```

---

## 8. **Undoing Changes**

### **Undo Changes Before Staging:**
```bash
git checkout -- <file>
```

### **Unstage Changes:**
```bash
git reset <file>
```

### **Revert a Commit:**
```bash
git revert <commit-hash>
```

---

## 9. **Useful Git Commands**

| Command                            | Description                                    |
|------------------------------------|------------------------------------------------|
| `git log`                          | View commit history                           |
| `git status`                       | Check the current status of the repo          |
| `git diff`                         | Show file changes not yet staged              |
| `git clone <repository-URL>`       | Clone a repository to your machine            |
| `git pull origin main`             | Pull the latest changes from GitHub           |
| `git push origin main`             | Push changes to GitHub                        |

---

## 10. **Tips for RHEL 9 Hosts**

- Use `screen` or `tmux` to keep your terminal session alive during long tasks.
- Install additional tools like `vim-enhanced` for better editing:
  ```bash
  sudo dnf install vim-enhanced -y
  ```
- Use SSH keys for GitHub authentication for secure and seamless access:
  ```bash
  ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
  ```

---

## 11. **Tips for Windows Hosts**

- Use **Git Bash** for a Unix-like terminal experience.
- Consider using **Visual Studio Code** for its built-in Git integration.
- Configure SSH keys:
  1. Generate a key:
     ```bash
     ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
     ```
  2. Add the key to the SSH agent:
     ```bash
     eval $(ssh-agent -s)
     ssh-add ~/.ssh/id_rsa
     ```

---

## 12. **Next Steps**

- Explore advanced Git features like `rebasing` and `stashing`.
- Learn collaboration techniques such as pull requests and resolving merge conflicts.
- Practice using Git workflows in real-world projects.

---

For further details, refer to:
- [Git Documentation](https://git-scm.com/doc)
- [Pro Git Book](https://git-scm.com/book/en/v2)
- [GitHub Guides](https://guides.github.com/)
