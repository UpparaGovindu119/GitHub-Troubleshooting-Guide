# Interview-Ready Troubleshooting Script

This is what you memorize and practice before an interview.

---

## 5-Step Troubleshooting Flow (Master This)

When interviewer asks: "How do you troubleshoot a problem?"

**Say this exactly:**

"I follow a systematic 5-step approach:

1. **Identify the exact error message** - Read it carefully, don't guess
2. **Check logs** - Go to the relevant log file and search for keywords like 'error', 'failed', 'denied'
3. **Verify current state** - Check directory, branch, permissions, disk space, network
4. **Identify root cause** - Connect the error message to the actual cause
5. **Fix and verify** - Apply the fix, then confirm it worked by checking logs again or running the command"

Then give a real example.

---

## Common Git/GitHub Errors (Memorize These 5)

### Error 1: `fatal: not a git repository`

**What to say in interview:**

"This error means I'm running git commands outside a Git repository folder.

**My 5 steps:**

1. Error message: `fatal: not a git repository`
2. Check logs: Terminal shows this directly
3. Verify state: I run `pwd` to check directory, then `ls -la` to check if `.git` folder exists
4. Root cause: I'm in the wrong folder or repo not initialized
5. Fix and verify: I run `git init` to initialize, or `git clone` to download the repo, then `git status` to confirm"

---

### Error 2: `Authentication failed` or `403`

**What to say in interview:**

"This error means Git cannot authenticate to GitHub - either credentials expired or SSH key is missing.

**My 5 steps:**

1. Error message: `fatal: Authentication failed for 'https://github.com/user/repo.git'`
2. Check logs: Terminal shows this directly. I check `git remote -v` to see the URL format (HTTPS or SSH)
3. Verify state: I run `ssh -T git@github.com` to test SSH, or check if PAT token exists
4. Root cause: Either SSH key not added to GitHub, or PAT token expired, or wrong credentials
5. Fix and verify: 
   - For SSH: Generate key with `ssh-keygen -t ed25519`, add to GitHub settings, test with `ssh -T git@github.com`
   - For HTTPS: Generate new PAT on GitHub.com, clear old credentials, authenticate again
   - Then `git pull origin main` to verify it works"

---

### Error 3: `Updates were rejected because the tip of your current branch is behind`

**What to say in interview:**

"This error means the remote branch has new commits that I don't have locally. I cannot push until I sync.

**My 5 steps:**

1. Error message: `! [rejected] main -> main (non-fast-forward)`
2. Check logs: Terminal shows this directly. I run `git status` and `git log --oneline -n 5` to compare
3. Verify state: I compare my commits with `git log origin/main --oneline -n 5`
4. Root cause: Remote branch is ahead of my local branch
5. Fix and verify:
   - I run `git fetch origin` to download latest
   - Then `git rebase origin/main` to replay my commits on top
   - Then `git push origin main` to push
   - Verify with `git log --oneline -n 5` showing my commits on top"

---

### Error 4: `conflict (content): Merge conflict in file.txt`

**What to say in interview:**

"This error means two branches changed the same file in the same place. I need to manually resolve it.

**My 5 steps:**

1. Error message: `CONFLICT (content): Merge conflict in file.txt`
2. Check logs: Terminal shows this. I run `git status` to see which files have conflicts
3. Verify state: I run `git diff` to see the conflict markers. I open the file and look for `<<<<<<<`, `=======`, `>>>>>>>`
4. Root cause: Both branches edited same lines, merge cannot auto-resolve
5. Fix and verify:
   - I manually edit the file to choose which changes to keep
   - Remove conflict markers
   - Run `git add file.txt`
   - Run `git commit -m 'Resolve merge conflict'`
   - Verify with `git status` (should be clean) and `git log --oneline -n 3` (should show merge commit)"

---

### Error 5: `fatal: unable to access 'https://...': Could not resolve host`

**What to say in interview:**

"This error means the network cannot reach GitHub. Either internet is down, DNS is broken, or URL is wrong.

**My 5 steps:**

1. Error message: `fatal: unable to access 'https://github.com/...': Could not resolve host`
2. Check logs: Terminal shows this directly
3. Verify state: I check `ping github.com` to test internet. I check `git remote -v` to verify URL is correct
4. Root cause: Either no internet connection, or DNS resolver not working, or wrong URL
5. Fix and verify:
   - I test internet with `ping 8.8.8.8`
   - I verify remote URL with `git remote -v`
   - I correct URL if needed with `git remote set-url origin https://github.com/user/repo.git`
   - Then `git fetch origin` to verify it works"

---

## Common npm Errors (Memorize These 3)

### Error 1: `npm: command not found`

**What to say in interview:**

"This means Node.js or npm is not installed.

**My 5 steps:**

1. Error message: `npm: command not found`
2. Check logs: Terminal shows this directly. I run `which npm`
3. Verify state: I check `node --version` and `npm --version`
4. Root cause: Node.js/npm not installed or PATH not configured
5. Fix and verify:
   - Install with `sudo apt-get install nodejs npm` (Ubuntu) or `brew install node` (Mac)
   - Verify with `node --version` and `npm --version`
   - Test with `npm install package-name`"

---

### Error 2: `npm ERR! 404 Not Found`

**What to say in interview:**

"This means the package doesn't exist or has wrong name.

**My 5 steps:**

1. Error message: `npm ERR! 404 Not Found - GET https://registry.npmjs.org/wrong-package`
2. Check logs: Terminal shows this. I check `~/.npm/_logs/` for detailed logs
3. Verify state: I check `cat package.json` to see package name
4. Root cause: Wrong package name or typo
5. Fix and verify:
   - Search for correct name: `npm search correct-package`
   - Install correct: `npm install correct-package`
   - Verify with `npm list correct-package`"

---

### Error 3: `EACCES: permission denied`

**What to say in interview:**

"This means npm trying to install globally without permission.

**My 5 steps:**

1. Error message: `EACCES: permission denied`
2. Check logs: Terminal shows this. I check `ls -l /usr/local/lib/node_modules`
3. Verify state: I check who owns the npm folder
4. Root cause: npm folder owned by root, npm trying to write without permission
5. Fix and verify:
   - Fix permissions: `mkdir ~/.npm-global && npm config set prefix '~/.npm-global'`
   - Export PATH: `export PATH=~/.npm-global/bin:$PATH`
   - Test: `npm install -g package-name`
   - Verify with `npm list -g`"

---

## Common Docker Errors (Memorize These 2)

### Error 1: `docker: command not found` or `permission denied`

**What to say in interview:**

"Either Docker not installed or user not in docker group.

**My 5 steps:**

1. Error message: `docker: command not found` or `Got permission denied`
2. Check logs: Terminal shows this. I run `which docker` and `groups`
3. Verify state: I check if docker group exists
4. Root cause: Docker not installed OR user not in docker group
5. Fix and verify:
   - Install: `sudo apt-get install docker.io` (Ubuntu)
   - Add user: `sudo usermod -aG docker $USER` then `newgrp docker`
   - Test: `docker run hello-world`"

---

### Error 2: `Bind for 0.0.0.0:8080 failed: port is already allocated`

**What to say in interview:**

"This means port 8080 is already in use by another container or app.

**My 5 steps:**

1. Error message: `Bind for 0.0.0.0:8080 failed: port is already allocated`
2. Check logs: Terminal shows this. I run `lsof -i :8080` to see what's using the port
3. Verify state: I check running containers with `docker ps`
4. Root cause: Another container or app using port 8080
5. Fix and verify:
   - Stop other container: `docker stop container-id`
   - OR use different port: `docker run -p 8081:8080 image-name`
   - Test: `curl localhost:8080`"

---

## Common GitHub Actions Errors (Memorize This 1)

### Error: `fatal: could not read Username for 'https://github.com'`

**What to say in interview:**

"Workflow trying to push without GitHub token configured.

**My 5 steps:**

1. Error message: `fatal: could not read Username`
2. Check logs: GitHub Actions workflow log in repo > Actions > job output
3. Verify state: I check `.github/workflows/ci.yml` to see if GITHUB_TOKEN is passed
4. Root cause: Workflow missing token in checkout step
5. Fix and verify:
   - Add to workflow YAML:
     ```yaml
     - uses: actions/checkout@v3
       with:
         token: ${{ secrets.GITHUB_TOKEN }}
     ```
   - Configure git user:
     ```yaml
     - run: |
         git config --global user.email 'action@github.com'
         git config --global user.name 'GitHub Action'
     ```
   - Re-run workflow and verify in Actions tab"

---

## What Logs to Check (Quick Reference)

| Tool | Where to Check | Command |
|------|------|------|
| **Git** | Terminal output | Direct - no file |
| **Git remote** | Terminal output | Direct - no file |
| **npm** | Terminal output + `~/.npm/_logs/` | `tail ~/.npm/_logs/*-debug-0.log` |
| **Docker** | Terminal output + `/var/log/docker.log` | `docker logs container-id` |
| **GitHub Actions** | GitHub.com > repo > Actions > job | Click on job to see logs |
| **System** | `/var/log/syslog` or `/var/log/messages` | `tail -f /var/log/syslog` |

---

## How to Verify After Fix (Quick Checklist)

After you apply a fix, always verify:

- [ ] Run the command again that previously failed
- [ ] Check the exact same logs for the error
- [ ] Confirm the success message or expected output
- [ ] Run a related command to confirm state

**Examples:**

- After Git auth fix: `git pull origin main`
- After npm install fix: `npm list package-name`
- After Docker fix: `docker run image-name`
- After GitHub Actions fix: Re-run workflow and check Actions tab

---

## Interview Answer Template

When interviewer asks: "Tell me about a time you debugged an issue"

**Use this format:**

"I encountered [error message]. 

**Here's how I debugged it:**

1. I read the exact error: '[error text]'
2. I checked the logs [where location]: and searched for 'error' or 'failed'
3. I verified the current state: [what I checked]
4. I identified the root cause: [what was actually wrong]
5. I fixed it by: [exact commands I ran]
6. I verified: [how I confirmed it worked]

The key was being systematic - not randomly trying commands, but understanding what the error was telling me."

---

## Practice This Now

Pick 3 errors from above and practice:
- Say the error message out loud
- Practice the 5-step explanation
- Say the exact commands you would run
- Record yourself and listen

When you interview, this will sound natural and professional.

---

## Final Interview Tips

✅ **DO say:**
- "I would check the exact error message"
- "Let me look at the relevant log file"
- "I would verify the current state first"
- "The root cause is..."
- "Let me verify this fix worked by..."

❌ **DON'T say:**
- "I Googled it and tried random commands"
- "I just ran some Git commands"
- "The error went away, but I'm not sure why"
- "I tried restarting until it worked"
- "I used sudo to force it"

---

## Confidence Checklist Before Interview

- [ ] I can explain the 5-step troubleshooting flow
- [ ] I can explain 5 Git/GitHub errors
- [ ] I can explain 3 npm errors
- [ ] I can explain 2 Docker errors
- [ ] I can explain 1 GitHub Actions error
- [ ] I know which logs to check for each
- [ ] I know how to verify after fix
- [ ] I can say it confidently without notes

If all checked - you are ready.
