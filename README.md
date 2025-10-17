# Managing Multiple GitHub Accounts - Complete Setup Guide (Windows, macOS & Linux)

This guide helps you set up and manage **multiple GitHub accounts** - personal, work, freelance, or more — cleanly on **Windows, macOS, or Linux**.

It covers:

1. [Fresh setup from scratch](#1-new-system-setup)
2. [Adding a new GitHub account to an existing setup](#2-adding-an-additional-github-account)
3. [Full cleanup and starting over](#3-full-cleanup--fresh-start)

## Prerequisites

- Git is installed (check using `git --version`)
- SSH is available
- You have login access for each GitHub account you want to add

## Quick Reference for SSH Folder

| OS                 | SSH Folder Path            |
| ------------------ | -------------------------- |
| Windows (Git Bash) | `C:\Users\<YourName>\.ssh` |
| macOS / Linux      | `~/.ssh`                   |

## 1. New System Setup

### Step 1: Generate SSH Keys for Each Account

Run the following for **each account**, replacing the email and filename:

```bash
ssh-keygen -t ed25519 -C "your_email@example.com" -f ~/.ssh/id_ed25519_<alias>
```

Example for 3 accounts:

```bash
ssh-keygen -t ed25519 -C "personal@example.com" -f ~/.ssh/id_ed25519_personal
ssh-keygen -t ed25519 -C "work@company.com" -f ~/.ssh/id_ed25519_work
ssh-keygen -t ed25519 -C "freelance@client.com" -f ~/.ssh/id_ed25519_freelance
```

### Step 2: Start SSH Agent and Add Keys

#### **Windows / Linux**

```bash
eval "$(ssh-agent -s)"

ssh-add ~/.ssh/id_ed25519_personal
ssh-add ~/.ssh/id_ed25519_work
ssh-add ~/.ssh/id_ed25519_freelance

# You can add all your account keys one by one
```

#### **macOS**

```bash
eval "$(ssh-agent -s)"

ssh-add --apple-use-keychain ~/.ssh/id_ed25519_personal
ssh-add --apple-use-keychain ~/.ssh/id_ed25519_work
ssh-add --apple-use-keychain ~/.ssh/id_ed25519_freelance

# You can add all your account keys one by one
```

### Step 3: Add Public Keys to GitHub

For each key:

```bash
cat ~/.ssh/id_ed25519_<alias>.pub
```

Copy the output > Add it under:  
**GitHub > Settings > SSH and GPG keys > New SSH key**

### Step 4: Configure `~/.ssh/config`

Open or create the SSH config file:

```bash
nano ~/.ssh/config
```

Add a block for each account:

```ssh-config
# Personal GitHub
Host github.com-personal
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519_personal

# Work GitHub
Host github.com-work
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519_work

# Freelance GitHub
Host github.com-freelance
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519_freelance
```

Tip: You can name the host anything (e.g., `github.com-client`, `github.com-organization`, etc.).

### Step 5: Set Up Git Identity Aliases

Edit your shell config:

- **Windows/Linux (Bash):** `~/.bashrc`
- **macOS (Zsh):** `~/.zshrc`

Add:

```bash
alias git-personal='git config user.name "Your Personal Name" && git config user.email "personal@example.com"'
alias git-work='git config user.name "Your Work Name" && git config user.email "work@company.com"'
alias git-freelance='git config user.name "Your Freelance Name" && git config user.email "freelance@client.com"'
```

Reload:

```bash
source ~/.bashrc   # or source ~/.zshrc for macOS if you use zsh instead of bash
```

### Step 6: Test SSH Setup

```bash
ssh -T git@github.com-personal
ssh -T git@github.com-work
ssh -T git@github.com-freelance
```

Expected Output:  
`Hi username! You've successfully authenticated, but GitHub does not provide shell access.`

### Step 7: Clone Repositories

| Account   | Example Clone Command                                |
| --------- | ---------------------------------------------------- |
| Personal  | `git clone git@github.com-personal:user/repo.git`    |
| Work      | `git clone git@github.com-work:org/repo.git`         |
| Freelance | `git clone git@github.com-freelance:client/repo.git` |

## 2. Adding an Additional GitHub Account

If you already have accounts configured and want to add another:

> NOTE -
>
> Use your preferred name in place of `<alias>` in all the above commands.

1. Generate a new SSH key

   ```bash
   ssh-keygen -t ed25519 -C "new_account@example.com" -f ~/.ssh/id_ed25519_<alias>
   ```

2. Add it to the SSH agent

   #### **Windows / Linux**

   ```bash
   eval "$(ssh-agent -s)"

   ssh-add ~/.ssh/id_ed25519_<alias>
   ```

   #### **macOS**

   ```bash
   eval "$(ssh-agent -s)"

   ssh-add --apple-use-keychain ~/.ssh/id_ed25519_<alias>
   ```

3. Update `~/.ssh/config`

   ```ssh-config
   Host github.com-<alias>
       HostName github.com
       User git
       IdentityFile ~/.ssh/id_ed25519_<alias>
   ```

4. Add key to GitHub > _Settings > SSH Keys > New SSH Key_

5. Clone using:

   ```bash
   git clone git@github.com-<alias>:user/repo.git
   ```

## 3. Full Cleanup & Fresh Start

If your setup is messy or broken, start over cleanly.

### Step 1: Stop ssh-agent

```bash
eval "$(ssh-agent -k)"
```

### Step 2: Backup & Remove Old SSH Files (If you want - it's optional)

```bash
mkdir -p ~/.ssh_backup
timestamp=$(date +"%Y%m%d_%H%M%S")
cp -r ~/.ssh ~/.ssh_backup/ssh_backup_$timestamp 2>/dev/null

rm -f ~/.ssh/id_ed25519*
rm -f ~/.ssh/config
rm -f ~/.ssh/known_hosts ~/.ssh/known_hosts.old
```

### Step 3: Reset Git Config

```bash
git config --global --unset user.name
git config --global --unset user.email
git config --global --unset user.signingkey
```

Then follow [New System Setup](#1-new-system-setup) steps.

## Updating Existing Repositories

If you cloned repos using an old alias (e.g. `github.com-work`), update them:

```bash
git remote set-url origin git@github.com-<alias>:organization/repo.git
```

## Summary Table

| Account   | Key File                      | Host Alias             | Example Clone URL                          |
| --------- | ----------------------------- | ---------------------- | ------------------------------------------ |
| Personal  | `~/.ssh/id_ed25519_personal`  | `github.com-personal`  | `git@github.com-personal:user/repo.git`    |
| Work      | `~/.ssh/id_ed25519_work`      | `github.com-work`      | `git@github.com-work:org/repo.git`         |
| Freelance | `~/.ssh/id_ed25519_freelance` | `github.com-freelance` | `git@github.com-freelance:client/repo.git` |
| New       | `~/.ssh/id_ed25519_new`       | `github.com-new`       | `git@github.com-new:user/repo.git`         |

## Common Commands

| Purpose                | Command                         |
| ---------------------- | ------------------------------- |
| List SSH keys loaded   | `ssh-add -l`                    |
| List global Git config | `git config --global --list`    |
| Check remote URL       | `git remote -v`                 |
| Test GitHub connection | `ssh -T git@github.com-<alias>` |

## Pro Tips

- Keep **one key per account**.
- Avoid using the same SSH key for multiple accounts.
- Use clear host aliases (`github.com-work`, `github.com-freelance`, etc.).
- If your organization uses **SSO/SAML**, authorize your SSH key under GitHub Org > Settings > SSH Keys.

## Quick Cleanup Script (All OS)

```bash
eval "$(ssh-agent -k)"

mkdir -p ~/.ssh_backup
timestamp=$(date +"%Y%m%d_%H%M%S")
cp -r ~/.ssh ~/.ssh_backup/ssh_backup_$timestamp 2>/dev/null

rm -f ~/.ssh/id_ed25519*
rm -f ~/.ssh/config
rm -f ~/.ssh/known_hosts ~/.ssh/known_hosts.old

git config --global --unset user.name
git config --global --unset user.email
git config --global --unset user.signingkey

echo "Clean slate ready. Generate fresh SSH keys and update ~/.ssh/config!"
```

## You're Done

You now have:

- Clean SSH setup
- Multiple GitHub accounts
- Easy alias-based identity switching
- Verified per-account remotes
