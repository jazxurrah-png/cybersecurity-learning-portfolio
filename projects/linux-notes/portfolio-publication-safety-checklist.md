# Public Portfolio Safety Checklist

## Never Publish

- Passwords
- Personal access tokens
- API keys
- SSH private keys
- Recovery codes
- Session cookies
- Authentication headers
- Private certificates
- `.env` files containing secrets

## Review Carefully

- Real names and email addresses
- Home addresses and phone numbers
- Public or local IP addresses
- Wi-Fi network names
- Serial numbers and MAC addresses
- Filesystem UUIDs
- Hostnames and Linux usernames
- Private directory paths
- Employer or customer information

## Search the Repository

```bash
grep -RIn "USERNAME" .
grep -RIn "@" --include='*.md' --include='*.txt' .
grep -RIniE 'password|passwd|token|secret|api[_-]?key|private[_-]?key' .
```

Review Git changes:

```bash
git status
git diff --staged
```

## Suggested `.gitignore` Entries

```gitignore
.env
.env.*
*.key
*.pem
*.p12
*.pfx
id_rsa
id_ed25519
secrets/
private/
```

## Important Git Behavior

Adding a file to `.gitignore` does not remove it from earlier commits.

Stop tracking a file while keeping it locally:

```bash
git rm --cached PATH_TO_FILE
git commit -m "Remove private file from repository"
```

A committed secret should be considered exposed and rotated even after deletion.

## Before Every Push

- [ ] Run `git status`
- [ ] Review `git diff --staged`
- [ ] Confirm no secrets are present
- [ ] Check screenshots for private data
- [ ] Add warnings to destructive commands
- [ ] Make sure claims match what was actually tested
- [ ] Use a clear commit message

## Portfolio Quality Check

- [ ] State the problem clearly
- [ ] Identify the environment
- [ ] Format commands in code blocks
- [ ] Separate results from assumptions
- [ ] Document failed attempts honestly
- [ ] Summarize the lesson learned
