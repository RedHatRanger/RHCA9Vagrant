Tutorial Video Link [Here](https://www.youtube.com/watch?v=kQyDWocyqHw)

# Lab 01: Understanding and Use Git

Here are the [EX374 Exam Objectives](https://www.redhat.com/en/services/training/red-hat-certified-specialist-developing-automation-ansible-automation-platform-exam?section=objectives):
1. **Cloning a Git Repository** from a remote host (In this case, GitHub).
2. **Creating, Modifying, and Pushing** files into Git.

---

## 1. Overview

This lab helps you practice fundamental Git operations for the Red Hat EX374 exam. You will:

1. Create or use an **existing GitHub repository**.
2. **Clone** the repo locally.
3. Create a **new branch**, add or change content (e.g., Ansible playbooks).
4. **Commit** and **push** changes back to GitHub.
5. Open and merge a **Pull Request** (optional but recommended).

The steps mirror what you'd do on an internal Git platform (GitLab/Gitea) and align with the study materials plus Eddie Jennings’s approach.

---

## 2. Prerequisites

1. **Git Installed** on your control node or workstation:

   ```bash
   sudo dnf install -y git
   ```

2. **GitHub Account**: Create one if you haven't at [github.com](https://github.com).

3. **SSH Keys (Recommended) or HTTPS**:

   - **Generate a new RSA 4096 key pair**:
     ```bash
     ssh-keygen -t rsa -b 4096
     ```
   - Copy the **public key** (\~/.ssh/id\_rsa.pub) into your GitHub user settings (**Settings** → **SSH and GPG keys** → **New SSH key**).

   *(If you prefer HTTPS, you can skip SSH key generation.)*

4. **Git Global Config**:

   ```bash
   git config --global user.name "Your Name"
   git config --global user.email "your_email@example.com"
   ```

---

## 3. Create a GitHub Repository

1. **On GitHub**, click the **+** (upper right) → **New Repository**.
2. Fill in:
   - Repository name: `ex374-git-lab` (example)
   - Description: "EX374 practice repo"
   - Choose **Public** or **Private**
   - *Optionally* add a simple README.
3. **Create Repository**.

---

## 4. Clone the Repository Locally

1. **Navigate** to your development directory:
   ```bash
   mkdir -p ~/dev
   cd ~/dev
   ```
2. **Clone** via SSH:
   ```bash
   git clone git@github.com:<your-username>/ex374-git-lab.git
   cd ex374-git-lab
   ```
   *(If using HTTPS, the URL will be ******`https://github.com/<your-username>/ex374-git-lab.git`******)*

---

## 5. **(Optional) Enhance your Bash Prompt to Show Git Status**:
   You can add a script that modifies your Bash prompt to display your current branch and changes. Add the following lines to your `~/.bashrc` (or `~/.bash_profile`):

   ```bash
   source /usr/share/git-core/contrib/completion/git-prompt.sh
   export GIT_PS1_SHOWDIRTYSTATE=true
   export GIT_PS1_SHOWUNTRACKEDFILES=true
   export PS1='[\u@\h \W$(declare -F __git_ps1 &>/dev/null && __git_ps1 " (%s)")]\\$ '
   ```

   Then run:
   ```bash
   source ~/.bashrc
   ```
---

## 6. Create and Modify Files

1. **Create a Branch**

   ```bash
   git checkout -b exercise
   ```

   This creates and switches to a new branch `exercise`.

2. **Add or Update Files**

   - Example: Create a simple Ansible playbook file, `apache-setup.yml`:
     ```yaml
     ---
     - name: Install and configure Apache
       hosts: web_servers
       become: true

       vars:
         httpd_packages:
           - httpd
           - python3-mod_wsgi

       tasks:
         - name: Install Apache and mod_wsgi
           package:
             name: "{{ httpd_packages }}"
             state: present

         - name: Ensure Apache is running
           service:
             name: httpd
             state: started
             enabled: true
     ```

3. **Stage and Commit**:

   ```bash
   git add apache-setup.yml
   git commit -m "Add Apache setup playbook"
   ```

---

## 7. Push and Merge

1. **Push Your Branch** to GitHub:

   ```bash
   git push --set-upstream origin exercise
   ```

2. **Open a Pull Request**

   - Go to your repository on GitHub.
   - Switch from `main` to the newly pushed `exercise` branch.
   - Click **Pull Request** → **Create Pull Request**.
   - Add a short description, then finalize.

3. **Merge** the Pull Request into `main`:

   - You can merge via GitHub’s web interface.
   - Optionally delete the `exercise` branch after merging.

4. \*\*Sync Local \*\***`main`**

   ```bash
   git checkout main
   git pull origin main
   ```

---

## 8. Verify and Cleanup

1. **Confirm** new file(s) exist on `main` branch in both your local repo and on GitHub.
2. **Remove** or keep the `exercise` branch locally:
   ```bash
   git branch -d exercise
   ```
3. (Optional) **Test** the playbook if you have an Ansible environment ready:
   ```bash
   ansible-playbook apache-setup.yml --check
   ```

---

## 9. Conclusion

You have now:

1. Created or selected a GitHub repository.
2. Cloned it locally.
3. Worked on a separate branch to add an Ansible playbook.
4. Pushed those changes and merged via a Pull Request.

This fulfills the EX374 objectives to **understand and use Git**, **clone a repo**, and **modify/push files**. Use these steps as a foundation for more advanced workflows, such as adding roles, hooking into CI/CD, or configuring additional branches.


<br><br><br><br>
## Git Cheat Sheet
![image](https://github.com/user-attachments/assets/1dad6084-b047-431f-8f64-291c2da4780e)

(Image subject to @Copyright by: https://in.linkedin.com/school/tutort/?trk=public_post-text)


<br><br>
And this one...
![image](https://github.com/user-attachments/assets/1ef44904-5c8d-4505-a2f2-6a9c9de1e59f)

(Image subject to @Copyright by: Danny Adams https://dev.to/doabledanny/git-cheat-sheet-50-commands-free-pdf-and-poster-4gcn/comments)
