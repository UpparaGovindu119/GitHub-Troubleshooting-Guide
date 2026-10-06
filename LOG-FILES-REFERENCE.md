# Log File Reference - Tool-Wise Error Identification

This file shows you EXACTLY where to look for errors in each tool's log files.
Follow the structure, find the error, identify it.

---

## 1) GIT LOG FILES

### Log File Location
```
Terminal output (direct - no file storage)
```

### Log File Structure
```
When you run git command:

$ git push origin main
fatal: Authentication failed for 'https://github.com/user/repo.git/'
```

### How to Capture Git Logs
```bash
# Git outputs directly to terminal
# To save for later:
git push origin main 2>&1 | tee git-error.log

# Then check:
cat git-error.log
grep -i "error\|failed\|fatal" git-error.log
```

### Common Log Patterns

**Pattern 1: Repository Error**
```
fatal: not a git repository (or any of the parent directories): .git
```
Search keyword: `fatal: not a git repository`
Root cause: Not in a Git folder

---

**Pattern 2: Authentication Error**
```
fatal: Authentication failed for 'https://github.com/user/repo.git/'
or
Permission denied (publickey).
fatal: Could not read from remote repository.
```
Search keyword: `Authentication failed` OR `Permission denied` OR `publickey`
Root cause: Wrong credentials or SSH key

---

**Pattern 3: Network Error**
```
fatal: unable to access 'https://github.com/user/repo.git': Could not resolve host
or
fatal: unable to access 'https://github.com/user/repo.git': Operation timed out
```
Search keyword: `Could not resolve host` OR `timed out`
Root cause: Internet issue or DNS

---

**Pattern 4: Push Rejection**
```
! [rejected]        main -> main (non-fast-forward)
error: failed to push some refs to 'https://github.com/user/repo.git'
hint: Updates were rejected because the tip of your current branch is behind
```
Search keyword: `rejected` OR `failed to push`
Root cause: Remote has newer commits

---

**Pattern 5: Merge Conflict**
```
CONFLICT (content): Merge conflict in file.txt
Automatic merge failed; fix conflicts and then commit the result.
```
Search keyword: `CONFLICT` OR `Merge conflict`
Root cause: Same file edited in both branches

---

### How to Check Git Logs

```bash
# Check last command output
git status

# Show commit history (log)
git log --oneline

# Show detailed history
git log --oneline --decorate --graph --all

# Check remote
git remote -v

# Check config
git config --list
```

---

## 2) NPM LOG FILES

### Log File Location
```
~/.npm/_logs/
```

### How to Access
```bash
# List all npm logs
ls -la ~/.npm/_logs/

# Show latest log
tail ~/.npm/_logs/*-debug-0.log

# Search in logs
grep -i "error\|failed" ~/.npm/_logs/*-debug-0.log
```

### Log File Structure

```
0 info it worked if it ends with ok
1 verbose cli /usr/bin/node /usr/local/bin/npm
2 info using npm@9.0.0
3 info using node@v18.0.0
4 verbose npm-session 1234567890abcdef
5 http request GET https://registry.npmjs.org/package-name
6 http 404 https://registry.npmjs.org/package-name
7 error code E404
8 error 404 Not Found - GET https://registry.npmjs.org/package-name
9 error 404
10 error 404 'package-name@latest' is not in this registry.
11 error 404 You should bug the author to publish it (or use the force flag)
...
13 error A complete log of this run can be found in: /home/user/.npm/_logs/2024-01-10T12_34_56_789Z-debug-0.log
```

### Common Log Patterns

**Pattern 1: Package Not Found (404)**
```
6 http 404 https://registry.npmjs.org/wrong-package-name
7 error code E404
8 error 404 Not Found - GET https://registry.npmjs.org/wrong-package-name
```
Search keyword: `404` OR `E404`
Root cause: Package doesn't exist or wrong name

---

**Pattern 2: Permission Denied**
```
gyp ERR! stack Error: EACCES: permission denied, mkdir '/usr/local/lib/node_modules'
5 error code EACCES
6 error syscall mkdir
7 error path /usr/local/lib/node_modules
```
Search keyword: `EACCES` OR `permission denied`
Root cause: Folder permission issue

---

**Pattern 3: Dependency Conflict**
```
npm error code ERESOLVE
npm error ERESOLVE unable to resolve dependency tree
npm error
npm error While resolving: myapp@1.0.0
npm error Found: react@17.0.0
npm error Could not find a version that is compatible with peer dep react@^18.0.0
```
Search keyword: `ERESOLVE` OR `unable to resolve`
Root cause: Package version conflict

---

**Pattern 4: Network Error**
```
npm http request GET https://registry.npmjs.org/package-name
5 http fetch GET https://registry.npmjs.org/package-name - timeout
npm error code ETIMEDOUT
npm error errno ETIMEDOUT
npm error network request to https://registry.npmjs.org/package-name timed out
```
Search keyword: `ETIMEDOUT` OR `timeout`
Root cause: Internet/network issue

---

### How to Check npm Logs

```bash
# Show npm logs
cat ~/.npm/_logs/*-debug-0.log

# Search for errors
grep -i "error\|failed\|E[A-Z0-9]*" ~/.npm/_logs/*-debug-0.log

# Follow real-time
npm install --verbose 2>&1 | tee npm-install.log

# Check npm config
npm config list

# Verify install
npm list package-name
```

---

## 3) DOCKER LOG FILES

### Log File Location
```
/var/log/docker.log          (system log)
Container logs (in memory)
```

### How to Access
```bash
# View container logs
docker logs container-id

# Follow container logs in real-time
docker logs -f container-id

# Show last 50 lines
docker logs --tail 50 container-id

# System docker log
sudo tail -f /var/log/docker.log

# Search in logs
docker logs container-id | grep -i "error\|failed"
```

### Log File Structure (Container)

```
$ docker run myimage

Starting application...
[INFO] Server running on port 8080
[ERROR] Cannot connect to database at localhost:5432
[ERROR] Connection refused
Application crashed with exit code 1
```

### Common Log Patterns

**Pattern 1: Application Error**
```
[ERROR] Cannot connect to database at localhost:5432
[ERROR] Connection refused
Application exited with code 1
```
Search keyword: `ERROR` OR `failed` OR `exit code`
Root cause: Application crashed inside container

---

**Pattern 2: Port Already in Use**
```
$ docker run -p 8080:8080 myimage
docker: Error response from daemon: driver failed programming external connectivity on endpoint 
bind: address already in use
```
Search keyword: `bind: address already in use` OR `port`
Root cause: Another container/app using the port

---

**Pattern 3: Permission Denied**
```
docker: Got permission denied while trying to connect to Docker daemon
```
Search keyword: `permission denied`
Root cause: User not in docker group

---

**Pattern 4: Out of Space**
```
Error response from daemon: error creating overlay2 mount to /var/lib/docker/overlay2/.../merged: no space left on device
```
Search keyword: `no space left`
Root cause: Disk full

---

### How to Check Docker Logs

```bash
# See running containers
docker ps

# See all containers (including stopped)
docker ps -a

# View container logs
docker logs container-id

# Follow logs in real-time
docker logs -f container-id

# Get container details
docker inspect container-id

# Check docker disk usage
docker system df

# Check container resource usage
docker stats container-id
```

---

## 4) GITHUB ACTIONS LOG FILES

### Log File Location
```
GitHub.com website (web interface)
Path: repo > Actions > workflow-name > job-name > step output
```

### How to Access
```
1. Go to: https://github.com/user/repo
2. Click: Actions tab
3. Click: workflow name that failed
4. Click: job name
5. Click: step that failed
6. Read: full output in step
```

### Log File Structure

```
Run: npm install
  npm install
  added 150 packages

Run: npm test
  npm test
  
  > myapp@1.0.0 test
  > jest
  
  FAIL src/app.test.js
    ✕ should initialize (10ms)
    
  ● should initialize
  
    ReferenceError: Cannot find module 'config'
    at Object.<anonymous> (src/app.js:5:3)
    at Module._load (internal/modules/require.js:463:13)

  Test Suites: 0 passed, 1 failed
  Tests: 0 passed, 1 failed
```

### Common Log Patterns

**Pattern 1: Build Failed**
```
Run: npm run build
error TS2322: Type 'string' is not assignable to type 'number'.
  22   const count: number = "5";
       ~~~~~~~~
```
Search keyword: `error` OR `failed` OR `TS[0-9]*`
Root cause: Code compilation error

---

**Pattern 2: Test Failed**
```
FAIL src/auth.test.js
  ✕ should authenticate user (15ms)
  
● should authenticate user

  Expected: "success"
  Received: "error"
```
Search keyword: `FAIL` OR `✕`
Root cause: Test assertion failed

---

**Pattern 3: Dependency Missing**
```
Run: npm install
npm ERR! 404 Not Found - GET https://registry.npmjs.org/missing-package
npm ERR! 404
```
Search keyword: `404` OR `not found`
Root cause: Package not installed

---

**Pattern 4: Token Error**
```
Run: git push origin main
fatal: could not read Username for 'https://github.com': No such file or directory
```
Search keyword: `could not read Username` OR `fatal`
Root cause: Missing GITHUB_TOKEN in workflow

---

### How to Check GitHub Actions Logs

```
Online only:

1. GitHub.com > repo > Actions tab
2. Click failed workflow
3. Click job name
4. Expand each step
5. Read output for 'error' or 'failed'
```

---

## 5) SYSTEM LOG FILES

### Log File Location
```
Linux:
/var/log/syslog        (Ubuntu/Debian)
/var/log/messages      (RedHat/CentOS)
/var/log/kern.log      (kernel messages)

macOS:
/var/log/system.log
~/Library/Logs/

Windows:
Event Viewer > Windows Logs > System
```

### How to Access
```bash
# View system log (Linux)
sudo tail -f /var/log/syslog

# Search for errors
sudo grep -i "error\|failed\|denied" /var/log/syslog

# Show last lines
sudo tail -n 100 /var/log/syslog

# Check disk space
df -h

# Check memory
free -h
```

### Common Log Patterns

**Pattern 1: Disk Full**
```
kernel: [12345.678901] EXT4-fs error (device sda1): ext4_mb_generate_buddy:788: group 5120 block bitmap corrupt
kernel: [12345.678902] JBD2: Spotted corrupted metadata block
...
No space left on device
```
Search keyword: `No space left` OR `disk full`
Root cause: Disk 100% full

---

**Pattern 2: Permission Denied**
```
sudo: user : command not allowed : /usr/local/bin/script.sh
```
Search keyword: `Permission denied` OR `not allowed`
Root cause: User lacks permission

---

**Pattern 3: Out of Memory**
```
kernel: Out of memory: Kill process pid (java) score 245 or sacrifice child
Killed process pid java total-vm:2048000kB
```
Search keyword: `Out of memory` OR `Killed process`
Root cause: RAM exhausted

---

## QUICK ERROR IDENTIFICATION FLOWCHART

```
Error happens
    ↓
Which tool? → Git → Check terminal output
            → npm → Check ~/.npm/_logs/
            → Docker → Check docker logs container-id
            → GitHub Actions → Check GitHub.com Actions tab
            → System → Check /var/log/syslog
    ↓
Search in log for:
- "error" or "failed"
- "denied" or "permission"
- "timeout" or "refused"
- specific error code (E404, EACCES, etc)
    ↓
Match pattern from this guide
    ↓
Identify root cause
    ↓
Apply fix
    ↓
Verify in logs again
```

---

## TEMPLATE: How to Find and Read Error

### Step 1: Know your tool
Is it Git? npm? Docker? GitHub Actions? System?

### Step 2: Go to log file location
| Tool | Location |
|------|----------|
| Git | Terminal (direct) |
| npm | ~/.npm/_logs/ |
| Docker | docker logs OR /var/log/docker.log |
| GitHub Actions | GitHub.com > Actions tab |
| System | /var/log/syslog |

### Step 3: Search for error keywords
```bash
grep -i "error\|failed\|denied\|timeout" logfile
```

### Step 4: Match the pattern
Compare with patterns in this guide.

### Step 5: Identify root cause
Connect error message to actual problem.

### Step 6: Fix it
Apply the fix from tool-wise-troubleshooting.md

### Step 7: Verify
Check logs again - error should be gone.

---

## INTERVIEW ANSWER

When asked: "How do you identify errors?"

"I follow this process:

1. **Identify the tool** - Is it Git, npm, Docker, or GitHub Actions?

2. **Go to the log file** - Each tool has a specific location:
   - Git: terminal output (direct)
   - npm: ~/.npm/_logs/
   - Docker: docker logs command
   - GitHub Actions: GitHub.com Actions tab
   - System: /var/log/syslog

3. **Search for keywords** - I grep for 'error', 'failed', 'denied', 'timeout'

4. **Match the pattern** - I look for patterns I've memorized:
   - 404 = package not found
   - EACCES = permission issue
   - timeout = network issue
   - Permission denied = auth issue

5. **Identify root cause** - The log tells me exactly what's wrong

6. **Fix and verify** - I apply the fix and check logs again to confirm"

This shows you are systematic and know where to look.
