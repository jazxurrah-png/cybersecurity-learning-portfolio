# Git and GitHub on Fedora Kinoite

## Objective

Create a local Git repository, make the first commit, authenticate with GitHub, and push the repository from Fedora Kinoite.

## Initializing the Repository

```bash
cd ~/Documents/james-cybersecurity-learning-portfolio
git init
git status
git add .
git commit -m "Initial portfolio upload"
git branch -M main
```

## Configuring Git Identity

```bash
git config --global user.name "YOUR NAME"
git config --global user.email "YOUR_GITHUB_EMAIL"
git config --global --list
```

## Connecting GitHub

```bash
git remote add origin https://github.com/USERNAME/REPOSITORY.git
git remote -v
```

If the remote already exists but is wrong:

```bash
git remote set-url origin https://github.com/USERNAME/REPOSITORY.git
```

## Authenticating on Kinoite

GitHub account passwords are not accepted for HTTPS Git operations. I used GitHub CLI with browser authentication.

```bash
sudo rpm-ostree install gh
systemctl reboot
```

After reboot:

```bash
gh auth login
gh auth status
```

Selected options:

- GitHub.com
- HTTPS
- Authenticate Git with GitHub credentials
- Log in with a web browser

## Pushing

```bash
git push -u origin main
```

Later updates:

```bash
git add .
git commit -m "Describe the change"
git push
```

## Errors Encountered

### Placeholder Username

Incorrect example URL:

```text
https://github.com/YOURUSERNAME/cybersecurity-learning-portfolio.git
```

Resolution: replace the placeholder with the real GitHub username using `git remote set-url`.

### Password Authentication Rejected

```text
Password authentication is not supported for Git operations.
```

Resolution: use GitHub CLI or a personal access token.

### `dnf` Not Available on the Host

```text
sudo: dnf: command not found
```

Resolution: install the host-integrated tool with `rpm-ostree` on Kinoite.

## Lessons Learned

- Git manages local history; GitHub hosts the remote repository.
- Remote URLs must contain the real username and repository name.
- GitHub HTTPS authentication requires a token or authenticated helper.
- Fedora Kinoite uses a different host package workflow from Workstation.
