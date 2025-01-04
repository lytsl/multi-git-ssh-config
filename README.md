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
```bash
ssh-keygen -t rsa -b 4096 -C "youremail@example.com"
```

- Save the key in a descriptive location:
  - Personal account: `~/.ssh/id_rsa_personal`
  - Work account: `~/.ssh/id_rsa_work`
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
   ssh-add ~/.ssh/id_rsa_personal
   ssh-add ~/.ssh/id_rsa_work
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
    IdentityFile ~/.ssh/id_rsa_personal

# Work GitHub Account
Host github-work
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_rsa_work
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
         email = personal@example.com
     ```
   - For work:
     ```bash
     nano ~/Work/.gitconfig
     ```
     ```plaintext
     [user]
         name = Your Work Name
         email = work@example.com
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