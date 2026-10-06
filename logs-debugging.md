# Logs and Debugging Guide

Logs are the most useful source when a command or application fails. If you do not read logs, you are guessing.

## 1) Why logs matter

Logs tell you:
- what command failed
- at what step it failed
- whether it is a permission issue, network issue, or parse issue
- whether the error is local or remote

Without logs, troubleshooting becomes random.

---

## 2) Basic commands for logs

### show file content
```bash
cat app.log
less app.log
more app.log
```

### show last lines
```bash
tail app.log
tail -n 50 app.log
```

### follow logs in real time
```bash
tail -f app.log
```

Use this during:
- server startup
- deployment
- app debug runs
- pipeline execution

---

### search error messages
```bash
grep -i "error" app.log
grep -i "failed" app.log
grep -i "exception" app.log
```

### search for specific text
```bash
grep -R "TODO" .
grep -R "localhost" .
```

---

## 3) Git history and debug logs

### show commit history
```bash
git log --oneline
```

### show recent changes
```bash
git log --oneline --decorate --graph --all
```

### check status before debugging
```bash
git status
```

### see actual file differences
```bash
git diff
git diff --cached
```

---

## 4) System and process logs

### check running processes
```bash
ps -ef
```

### check memory and CPU
```bash
top
free -h
```

### check disk use
```bash
df -h
du -sh .
```

### check permissions
```bash
ls -l
```

---

## 5) How to debug with logs step by step

1. Reproduce the problem
2. Capture the exact error message
3. Check the last 20-50 lines of the log
4. Search for keywords like `error`, `failed`, `exception`, `denied`
5. Check related files and config
6. Fix the root cause
7. Re-run and confirm the issue is gone

---

## 6) Example debugging pattern

Suppose deployment fails.

```bash
tail -n 50 .github/workflows/deploy.yml
```

Or:

```bash
tail -n 50 /var/log/app.log
```

Then:
```bash
grep -i "error\|failed\|denied" /var/log/app.log
```

This tells you whether the issue is:
- permission
- missing env variable
- remote server unreachable
- dependency missing
- build script broken

---

## 7) Common log-debugging mistakes

- ignoring the first line of the error
- reading only the top of the file instead of the tail
- searching for the wrong keyword
- not checking branch/status before debugging
- making random changes without understanding the log

---

## 8) Good habits for troubleshooting

- keep logs from failures
- save command output when debugging
- use `tail -f` for live issues
- verify one fix at a time
- prefer root-cause fixes over random commands

---

## 9) The most important principle

Logs are not just messages; they are clues.

If the log says:
- `permission denied` → fix permissions
- `no space left` → free disk
- `repository not found` → check remote URL or access
- `merge conflict` → resolve conflict manually
- `authentication failed` → recheck credentials

The right fix is almost always connected to the exact log message.
