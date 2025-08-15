# 🌐 Multi Git SSH Config

## 🚀 Introduction
This repository provides a comprehensive guide to configuring multiple Git accounts on the same computer using SSH and folder-based strategies. Perfect for developers managing personal, work, or organization-specific Git accounts. 🧑‍💻

---

## 🎯 Goal
The goal is to:
- Separate repositories by account using folder structures.
- Configure SSH for seamless authentication with multiple Git accounts.
- Automate Git user configuration for specific directories.

---

## 🛠️ Strategy Overview
1. **Organize repositories into folders**:
   - Example: `~/Personal` for personal repos, `~/Work` for work repos.
2. **Set up SSH keys** for each account.
3. **Use Git's `includeIf` directive** to manage Git user settings automatically.

---

## 📋 Steps to Configure

### 1️⃣ Generate SSH Keys 🔑
Run the following command to generate an SSH key for each Git account:
```shell
ssh-keygen -t ed25519 -C "your_email@example.com"
```

> [!NOTE]
> If you are using a legacy system that doesn't support the Ed25519 algorithm, use:
>
> ```shell
> ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
> ```

This creates a new SSH key, using the provided email as a label.

- Save the key in a descriptive location:
  - Personal account: `~/.ssh/id_personal`
  - Work account: `~/.ssh/id_work`
- Optionally, add a passphrase for extra security.

---

### 2️⃣ Start the SSH Agent ⚡
To use your SSH keys, you need to start the SSH agent and add your keys to it.

1. Start the SSH agent:
   ```bash
   eval "$(ssh-agent -s)"
   ```
   This command will start the agent and return a process ID, like this:
   ```
   Agent pid 12345
   ```

2. Add your keys to the agent:
   ```bash
   ssh-add ~/.ssh/id_personal
   ssh-add ~/.ssh/id_work
   ```

3. Verify that your keys are loaded:
   ```bash
   ssh-add -l
   ```
   This should list the keys you’ve added.

---

### 3️⃣ Configure SSH 🚀
Edit your SSH configuration file:
```bash
nano ~/.ssh/config
```
Add entries for each Git account:
```plaintext
# Personal GitHub Account
Host github-personal
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_personal

# Work GitHub Account
Host github-work
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_work
```

Save and test each configuration:
```bash
ssh -T github-personal
ssh -T github-work
```

---

### 4️⃣ Configure Git Settings ⚙️
Set up user-specific settings for each account using Git's `includeIf` directive.

1. Edit your global Git configuration:
   ```bash
   nano ~/.gitconfig
   ```
2. Add the following lines to link specific folders to specific settings:
   ```plaintext
   [includeIf "gitdir:~/Personal/**"]
       path = ~/Personal/.gitconfig

   [includeIf "gitdir:~/Work/**"]
       path = ~/Work/.gitconfig
   ```
3. Create the local `.gitconfig` files:
   - For personal:
     ```bash
     nano ~/Personal/.gitconfig
     ```
     ```plaintext
     [user]
         name = Your Personal Name
         email = personal@users.noreply.github.com
         signingkey = ~/.ssh/id_personal.pub
     [github]
         user = personal-github-username
     [gpg]
         format = ssh
     [commit]
         gpgsign = true
     [core]
        sshCommand = ssh -i ~/.ssh/id_personal
     ```
   - For work:
     ```bash
     nano ~/Work/.gitconfig
     ```
     ```plaintext
     [user]
         name = Your Work Name
         email = work@users.noreply.github.com
         signingkey = ~/.ssh/id_work.pub
     [github]
         user = work-github-username
     [gpg]
         format = ssh
     [commit]
         gpgsign = true
     [core]
        sshCommand = ssh -i ~/.ssh/id_work
     ```

---

### 5️⃣ Cloning Repositories 🌀
When cloning a repository, use the appropriate SSH alias:
- Personal:
  ```bash
  git clone git@github-personal:username/repo.git
  ```
- Work:
  ```bash
  git clone git@github-work:username/repo.git
  ```

---

## 📂 Folder Structure Example
Here’s an example of how your folder structure might look:
```plaintext
~/Personal/
    ├── Repo1/
    ├── Repo2/
~/Work/
    ├── ClientProject1/
    ├── InternalTool/
```

---

## 🛡️ Testing the Setup
To verify everything is configured correctly:
1. Check the active Git configuration in a repository:
   ```bash
   git config --get user.name
   git config --get user.email
   ```
2. Push a commit and ensure it appears under the correct account on GitHub.

---

## 🌟 Why This Setup?
- **Separation:** Cleanly separate work and personal Git repositories.
- **Security:** Manage multiple SSH keys with ease.
- **Automation:** Avoid manual changes to Git configuration for every repo.

## ❓ Common Questions

### 1️⃣ Does this configuration support both GitLab and GitHub? 🤔
Yes, absolutely! 🎉 This setup works for any Git hosting platform that uses SSH for authentication, including:
- **GitHub**
- **GitLab**
- **Bitbucket**

You just need to add the appropriate SSH key and host configuration in your `~/.ssh/config`. For example, for GitLab:
```plaintext
# GitLab Account
Host gitlab-personal
    HostName gitlab.com
    User git
    IdentityFile ~/.ssh/id_gitlab_personal
```
Then use the corresponding host (`gitlab-personal`) when cloning or interacting with your GitLab repositories:
```bash
git clone git@gitlab-personal:username/repo.git
```

---

### 2️⃣ What if I forget to start the SSH agent? ⚡
If you try to interact with a repository and get a **"Could not open a connection to your authentication agent"** error, it means the SSH agent isn't running. Simply start the agent with:
```bash
eval "$(ssh-agent -s)"
```
Then add your keys again:
```bash
ssh-add ~/.ssh/id_personal
ssh-add ~/.ssh/id_work
```

To avoid doing this every time, you can automate it by adding the following to your `~/.bashrc` or `~/.zshrc`:
```bash
eval "$(ssh-agent -s)" > /dev/null
ssh-add ~/.ssh/id_personal &>/dev/null
ssh-add ~/.ssh/id_work &>/dev/null
```

---

### 3️⃣ Can I use HTTPS instead of SSH? 🌐
Yes, you can use HTTPS for Git, but it won't benefit from the SSH-specific configuration described in this guide. If you prefer HTTPS, you'll need to authenticate manually (or via a credential manager) for each account and repository.

---


### 4️⃣ How do I know which account is being used? 🕵️
Run the following commands in your repository to check the active configuration:
```bash
git config --get user.name  # Shows the configured user name
git config --get user.email # Shows the configured email
```
For SSH, you can check which key is being used by testing the SSH connection:
```bash
ssh -T github-personal
```
The response will tell you which account is being authenticated.

---

### 5️⃣ Can I use this setup on Windows? 🪟
Yes, this setup works on **Windows** using **WSL (Windows Subsystem for Linux)** or Git Bash. The only difference might be the file paths:
- Use `/c/Users/YourName/.ssh/` instead of `~/.ssh/` for Git Bash.
- WSL follows the same structure as Linux (`~/.ssh/`).
