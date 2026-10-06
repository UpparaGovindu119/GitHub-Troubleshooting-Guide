# Troubleshooting Guide

This guide helps you fix common Git, GitHub, terminal, and system issues with a calm step-by-step approach.

## 1) General troubleshooting workflow

When something breaks, follow this order:

1. Read the exact error message
2. Check the current folder and branch
3. Check logs
4. Check permissions and disk space
5. Check network and remote configuration
6. Fix one issue at a time
7. Verify the result

Never guess. A small mistake in path, branch, or auth can cause big confusion.

---

## 2) Common problems and fixes

### Problem: `Permission denied`
Cause:
- file/folder lacks execute or write permission
- trying to write to protected directory
- not using sudo when needed

Check:
```bash
ls -l
chmod +x script.sh
sudo chown user:user file.txt
```

Fix:
- grant correct permissions
- run commands with the right user

---

### Problem: `ls` shows nothing or wrong folder
Cause:
- wrong directory
- repository not cloned in the expected place

Check:
```bash
pwd
ls
cd ..
```

Fix:
- move to the correct folder
- verify you are in the repository root

---

### Problem: `git: command not found`
Cause:
- Git is not installed
- shell PATH is not configured

Fix:
```bash
git --version
```

If missing:
- install Git
- reopen terminal or restart shell

---

### Problem: `fatal: not a git repository`
Cause:
- run git command outside a Git repo
- repository is not initialized or cloned

Fix:
```bash
git init
```
Or:
```bash
git clone <url>
```

---

### Problem: `fatal: unable to access 'https://...': Could not resolve host`
Cause:
- no internet connection
- DNS issue
- wrong URL

Fix:
- check network
- verify remote URL
- test with:
```bash
ping github.com
curl -I https://github.com
```

---

### Problem: `Repository not found` or `remote: Repository not found`
Cause:
- wrong repo URL
- wrong username or org name
- repo is private and you are not allowed

Fix:
```bash
git remote -v
git remote set-url origin https://github.com/<user>/<repo>.git
```

Check:
- your access permissions
- whether the repo exists
- whether the remote branch name is correct

---

### Problem: `Authentication failed` or `403`
Cause:
- expired credentials
- wrong PAT/token
- SSO not authorized
- wrong SSH key

Fix:
- re-authenticate GitHub
- check PAT permissions
- verify SSH key is added to GitHub
- test:
```bash
ssh -T git@github.com
```

---

### Problem: `fatal: refusing to merge unrelated histories`
Cause:
- two repositories with different histories are being merged

Fix:
```bash
git merge origin/main --allow-unrelated-histories
```

Use only if you intentionally want this merge.

---

### Problem: `Merge conflict`
Cause:
- same file changed in multiple places
- both branches modified same lines

Fix:
```bash
git status
git diff
```

Then:
- open conflicted files
- manually resolve the conflict
- mark as resolved:
```bash
git add <resolved-file>
git commit
```

Important:
- do not commit conflicting markers left in files

---

### Problem: `Your branch is behind 'origin/main' and can be fast-forwarded`
Cause:
- remote branch has newer commits

Fix:
```bash
git pull origin main
```

Or if using rebase:
```bash
git pull --rebase origin main
```

---

### Problem: `Updates were rejected because the tip of your current branch is behind`
Cause:
- remote has commits that you do not have locally

Fix:
```bash
git fetch origin
git rebase origin/main
```

Then:
```bash
git push origin main
```

---

### Problem: `error: failed to push some refs`
Cause:
- remote changes exist
- branch is behind
- local branch is not in sync

Fix:
```bash
git pull --rebase origin main
git push origin main
```

---

### Problem: `No space left on device`
Cause:
- disk is full

Check:
```bash
df -h
du -sh .
```

Fix:
- clean up temp files
- delete large cache directories
- free disk space before running builds or Git operations

---

### Problem: GitHub Actions or CI builds fail
Cause:
- missing environment variables
- permission issues
- dependency installation failure
- build script error

Fix:
- read workflow logs
- check if required secrets exist
- verify command syntax in YAML
- run the same command locally first

---

## 3) Real-world troubleshooting mindset

Most issues usually come from one of these:
- wrong directory
- wrong branch
- hidden local changes
- remote sync mismatch
- missing permissions
- full disk
- expired auth
- corrupted merge state

## 4) Quick rescue commands

```bash
git status
git branch
git remote -v
df -h
du -sh .
ls -l
cat .git/config
```

These commands reveal the state of your repo and the system.

## 5) Best practices

- Read error messages carefully
- Never hide warnings
- Run `git status` before making changes
- Check branch before push
- Check remote URL before cloning or pushing
- Keep logs for debugging
- Do not use `git reset --hard` without understanding the consequences

## 6) Final advice

Troubleshooting is a process, not a guess.

Whenever you see a problem:
- confirm the current state
- isolate the issue
- fix one cause
- test again

This method avoids random commands and prevents accidental damage.
