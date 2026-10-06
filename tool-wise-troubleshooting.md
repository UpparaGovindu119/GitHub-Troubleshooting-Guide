# Tool-Wise Troubleshooting Guide

This guide is organized by TOOL, not by error type. Each tool section has:
- What the tool is
- How to install it
- Prerequisites
- Common errors
- Log file locations
- How to debug
- How to verify

This prevents confusion about which tool causes which problem.

---

## 1) Git

### What is Git?
Version control system for tracking code changes.

### Prerequisites
- Linux/Mac/Windows OS
- Terminal/Command Prompt
- Internet connection

### Installation

**On Ubuntu/Debian:**
```bash
sudo apt-get update
sudo apt-get install git
git --version
```

**On macOS (with Homebrew):**
```bash
brew install git
git --version
```

**On Windows:**
- Download from https://git-scm.com/download/win
- Run installer
- Open Git Bash
- Run: `git --version`

### Verify installation
```bash
git --version
# Should show: git version 2.x.x
git config --list
```

---

### Common Git Errors

#### Error 1: `fatal: not a git repository`

**When it happens:**
- Running git commands outside a repository folder

**Log file:**
- Terminal output (direct)

**How to identify:**
```bash
pwd
ls -la
# If no .git folder, you are not in a repo
```

**Root cause:**
- Wrong directory
- Repository not cloned or initialized

**Fix:**
```bash
# Option 1: Initialize new repo
git init

# Option 2: Clone existing repo
git clone https://github.com/user/repo.git
cd repo
```

**Verify:**
```bash
git status
# Should show: On branch main
```

---

#### Error 2: `fatal: unable to access 'https://...': Could not resolve host`

**When it happens:**
- No internet connection
- DNS issue
- Wrong URL

**Log file:**
- Terminal output (direct)

**How to identify:**
```bash
ping github.com
curl -I https://github.com
```

**Root cause:**
- Internet down
- Firewall blocking
- Wrong remote URL

**Fix:**
```bash
# Check internet
ping 8.8.8.8

# Verify remote URL
git remote -v

# Correct if needed
git remote set-url origin https://github.com/user/repo.git

# Try again
git fetch origin
```

**Verify:**
```bash
git pull origin main
# Should succeed
```

---

#### Error 3: `Authentication failed` or `403`

**When it happens:**
- Expired credentials
- Wrong token/password
- SSH key missing
- Permission denied

**Log file:**
- Terminal output (direct)
- SSH log: `~/.ssh/id_rsa`
- Git credentials: `~/.git-credentials`

**How to identify:**
```bash
# For HTTPS
git remote -v
# Should show https://... if using HTTPS

# For SSH
ssh -T git@github.com
# Should return: successfully authenticated

# Check stored credentials
cat ~/.git-credentials
```

**Root cause:**
- Expired Personal Access Token (PAT)
- Wrong password
- Missing SSH key
- No access permission

**Fix option 1 (HTTPS with new PAT):**
```bash
# 1. Generate new PAT on GitHub.com
# Settings > Developer settings > Personal access tokens > Tokens (classic) > Generate new token

# 2. Clear old credentials
git credential-osxkeychain erase
# (on Linux: git credential-cache exit)

# 3. Try pull - enter new PAT when prompted
git pull origin main
```

**Fix option 2 (SSH):**
```bash
# 1. Generate SSH key
ssh-keygen -t ed25519 -C "your_email@example.com"

# 2. Show public key
cat ~/.ssh/id_ed25519.pub

# 3. Add to GitHub.com
# Settings > SSH and GPG keys > New SSH key > paste above

# 4. Change remote to SSH
git remote set-url origin git@github.com:user/repo.git

# 5. Test
ssh -T git@github.com
```

**Verify:**
```bash
# For HTTPS
git pull origin main

# For SSH
ssh -T git@github.com
git pull origin main
```

---

#### Error 4: `conflict (content): Merge conflict in file.txt`

**When it happens:**
- Same file edited in two branches
- Cannot auto-merge

**Log file:**
- Terminal output (direct)
- Git status: `git status`
- Conflict markers in file: `cat file.txt`

**How to identify:**
```bash
git status
# Look for: both modified: file.txt

git diff
# Shows differences

cat file.txt
# Look for markers:
# <<<<<<< HEAD
# =======
# >>>>>>> branch-name
```

**Root cause:**
- Two branches changed same lines
- Merge algorithm cannot decide

**Fix:**
```bash
# 1. Open the file
vim file.txt

# 2. Manually resolve by choosing which changes to keep
# 3. Remove conflict markers
# 4. Save file

# 5. Stage resolved file
git add file.txt

# 6. Complete merge
git commit -m "Resolve merge conflict"
```

**Verify:**
```bash
git status
# Should show clean status

git log --oneline -n 3
# Should show merge commit
```

---

#### Error 5: `Updates were rejected because the tip of your current branch is behind`

**When it happens:**
- Remote has new commits
- Cannot push

**Log file:**
- Terminal output (direct)

**How to identify:**
```bash
git status
git log --oneline -n 3
git log --oneline origin/main -n 3
# Compare - remote has more commits
```

**Root cause:**
- Remote branch has newer commits
- You need to sync first

**Fix:**
```bash
# Option 1: Rebase (cleaner history)
git fetch origin
git rebase origin/main
git push origin main

# Option 2: Merge (creates merge commit)
git pull origin main
git push origin main
```

**Verify:**
```bash
git push origin main
# Should succeed

git log --oneline -n 3
# Should show your commits on top
```

---

### Important Git Commands for Troubleshooting

```bash
# Check current state
git status
git branch
git log --oneline -n 5

# Check remote
git remote -v

# Check credentials
ssh -T git@github.com
git credential-osxkeychain get host=github.com

# Undo things
git reset --soft HEAD~1  # Undo last commit, keep changes
git reset --hard HEAD~1  # Undo last commit, lose changes (CAREFUL!)
git revert HEAD          # Create new commit that undoes last commit

# Clean up
git clean -fd           # Remove untracked files
git stash              # Save uncommitted changes temporarily
```

---

## 2) Node.js and npm

### What is Node.js?
JavaScript runtime for running JavaScript outside browser.

### What is npm?
Node Package Manager - tool for managing JavaScript libraries.

### Prerequisites
- Linux/Mac/Windows OS
- Terminal/Command Prompt
- Internet connection

### Installation

**On Ubuntu/Debian:**
```bash
sudo apt-get update
sudo apt-get install nodejs npm
node --version
npm --version
```

**On macOS (with Homebrew):**
```bash
brew install node
node --version
npm --version
```

**On Windows:**
- Download from https://nodejs.org/
- Choose LTS version
- Run installer
- Open PowerShell/Command Prompt
- Verify: `node --version` and `npm --version`

### Verify installation
```bash
node --version
# Should show: v18.x.x or similar

npm --version
# Should show: 9.x.x or similar

npm config list
# Shows npm settings
```

---

### Common Node.js/npm Errors

#### Error 1: `npm: command not found` or `node: command not found`

**When it happens:**
- Node/npm not installed
- PATH not configured

**Log file:**
- Terminal output (direct)

**How to identify:**
```bash
which node
which npm
echo $PATH
```

**Root cause:**
- Node.js/npm not installed
- Shell PATH missing the installation directory

**Fix:**
```bash
# Reinstall Node.js
# See installation section above

# Or add to PATH manually (rare)
export PATH="$PATH:/usr/local/bin"
```

**Verify:**
```bash
node --version
npm --version
```

---

#### Error 2: `npm ERR! 404 Not Found - GET https://registry.npmjs.org/...`

**When it happens:**
- Package does not exist
- Wrong package name
- Typo in package name

**Log file:**
- Terminal output (direct)
- npm debug log: `~/.npm/_logs/`

**How to identify:**
```bash
cat ~/.npm/_logs/*-debug-0.log
# Search for: 404 Not Found
```

**Root cause:**
- Package name is wrong
- Package was deleted
- Wrong registry configured

**Fix:**
```bash
# Check package name
npm search package-name

# Install correct package
npm install correct-package-name

# Or verify registry
npm config get registry
# Should be: https://registry.npmjs.org
```

**Verify:**
```bash
npm list package-name
# Should show installed version
```

---

#### Error 3: `EACCES: permission denied`

**When it happens:**
- npm trying to install globally without permission
- File permission issue

**Log file:**
- Terminal output (direct)
- npm debug log: `~/.npm/_logs/`

**How to identify:**
```bash
ls -l /usr/local/lib/node_modules
# Check permissions
```

**Root cause:**
- npm global folder owned by root
- npm trying to write without permission

**Fix option 1 (Fix npm permissions):**
```bash
mkdir ~/.npm-global
npm config set prefix '~/.npm-global'
export PATH=~/.npm-global/bin:$PATH
```

**Fix option 2 (Use sudo - not recommended):**
```bash
sudo npm install -g package-name
```

**Verify:**
```bash
npm list -g
# Should list global packages
```

---

#### Error 4: `npm ERR! code ERESOLVE, unable to resolve dependency tree`

**When it happens:**
- Conflicting package versions
- Dependency incompatibility

**Log file:**
- Terminal output (direct)
- npm debug log: `~/.npm/_logs/`

**How to identify:**
```bash
npm install
# Full error shows conflicting versions

cat package.json
# Check versions manually
```

**Root cause:**
- Two packages need different versions of same dependency
- Package versions not compatible

**Fix option 1 (Use legacy resolver):**
```bash
npm install --legacy-peer-deps
```

**Fix option 2 (Update packages):**
```bash
npm update
npm audit fix
```

**Fix option 3 (Manual fix):**
```bash
# Edit package.json to compatible versions
# Then:
npm install
```

**Verify:**
```bash
npm list
# Should show no red errors

node -e "console.log('OK')"
# Should print: OK
```

---

#### Error 5: `node_modules missing or ENOENT`

**When it happens:**
- node_modules folder deleted
- Not cloned with git
- Installation incomplete

**Log file:**
- Terminal output (direct)

**How to identify:**
```bash
ls -la
# Is node_modules folder there?

npm list
# Shows installed packages
```

**Root cause:**
- node_modules not installed
- .gitignore excludes it (correct)
- Installation interrupted

**Fix:**
```bash
# Reinstall dependencies
npm install

# Or clean and reinstall
rm -rf node_modules package-lock.json
npm install
```

**Verify:**
```bash
ls -la node_modules
# Should exist

npm list
# Should show tree of packages
```

---

### Important npm Commands for Troubleshooting

```bash
# Check installation
npm --version
npm config list

# Check what's installed
npm list
npm list -g              # Global packages
npm list package-name    # Specific package

# Debug
npm debug
npm install --verbose   # Verbose output

# Cache
npm cache clean --force
npm cache verify

# Audit
npm audit
npm audit fix

# Update
npm update
npm outdated

# Remove
npm uninstall package-name
npm prune
```

---

## 3) Docker

### What is Docker?
Containerization platform - runs apps in isolated environments.

### Prerequisites
- Linux/Mac/Windows OS with virtualization support
- At least 2GB RAM
- Internet connection

### Installation

**On Ubuntu/Debian:**
```bash
sudo apt-get update
sudo apt-get install docker.io
sudo usermod -aG docker $USER
newgrp docker
docker --version
```

**On macOS:**
```bash
# Download Docker Desktop from https://www.docker.com/products/docker-desktop
# Or use Homebrew:
brew install docker
docker --version
```

**On Windows:**
- Download Docker Desktop from https://www.docker.com/products/docker-desktop
- Run installer
- Restart computer
- Open PowerShell
- Verify: `docker --version`

### Verify installation
```bash
docker --version
# Should show: Docker version 20.x.x or similar

docker run hello-world
# Should print: Hello from Docker!
```

---

### Common Docker Errors

#### Error 1: `docker: command not found` or `permission denied`

**When it happens:**
- Docker not installed
- User not in docker group
- Socket permission issue

**Log file:**
- Terminal output (direct)
- Docker daemon log: `/var/log/docker.log`

**How to identify:**
```bash
which docker
whoami
groups
# Check if 'docker' group listed
```

**Root cause:**
- Docker not installed
- User not in docker group
- Docker daemon not running

**Fix option 1 (Install Docker):**
```bash
# See installation section above
```

**Fix option 2 (Add user to group):**
```bash
sudo usermod -aG docker $USER
newgrp docker
```

**Fix option 3 (Start daemon):**
```bash
sudo systemctl start docker
sudo systemctl enable docker
```

**Verify:**
```bash
docker run hello-world
# Should succeed
```

---

#### Error 2: `Error response from daemon: OCI runtime error`

**When it happens:**
- Container fails to run
- Runtime error inside container

**Log file:**
- Terminal output (direct)
- Container logs: `docker logs container-id`

**How to identify:**
```bash
docker ps -a
# Find container ID

docker logs container-id
# Full error message
```

**Root cause:**
- Application inside container crashed
- Entrypoint script failed
- Missing dependencies in image

**Fix:**
```bash
# See full logs
docker logs container-id

# Rebuild image
docker build -t image-name .

# Or run with bash to debug
docker run -it image-name /bin/bash
```

**Verify:**
```bash
docker run image-name
# Should start successfully

docker logs container-id
# Should show app output, no errors
```

---

#### Error 3: `Error: No space left on device`

**When it happens:**
- Docker disk space full
- Images/containers using too much space

**Log file:**
- Terminal output (direct)
- System logs: `/var/log/docker.log`

**How to identify:**
```bash
docker system df
# Shows Docker disk usage

df -h
# Shows overall disk usage

du -sh /var/lib/docker
# Shows Docker folder size
```

**Root cause:**
- Too many images
- Too many stopped containers
- Build cache too large

**Fix:**
```bash
# Remove unused containers
docker container prune -f

# Remove unused images
docker image prune -f

# Remove unused volumes
docker volume prune -f

# Clean everything (careful!)
docker system prune -a
```

**Verify:**
```bash
docker system df
# Should show reduced usage

docker run hello-world
# Should work
```

---

#### Error 4: `Bind for 0.0.0.0:8080 failed: port is already allocated`

**When it happens:**
- Port already in use
- Another container/app using same port

**Log file:**
- Terminal output (direct)

**How to identify:**
```bash
netstat -an | grep 8080
# Shows processes using port 8080

lsof -i :8080
# Lists processes on port 8080
```

**Root cause:**
- Another container using port
- Another application using port
- Port not released after container stop

**Fix option 1 (Use different port):**
```bash
docker run -p 8081:8080 image-name
# Map 8081 on host to 8080 in container
```

**Fix option 2 (Stop other container):**
```bash
docker stop container-id
docker rm container-id

docker run -p 8080:8080 image-name
```

**Fix option 3 (Kill process):**
```bash
lsof -i :8080
# Note the PID

kill -9 PID
```

**Verify:**
```bash
docker run -p 8080:8080 image-name
# Should start

curl localhost:8080
# Should respond
```

---

### Important Docker Commands for Troubleshooting

```bash
# Check status
docker --version
docker info
docker ps              # Running containers
docker ps -a           # All containers
docker images          # All images

# Logs
docker logs container-id
docker logs -f container-id    # Follow logs
docker logs --tail 50 container-id

# Debug
docker exec -it container-id /bin/bash  # Enter container
docker inspect container-id             # Full container details
docker system df                        # Disk usage

# Clean up
docker rm container-id
docker rmi image-id
docker container prune
docker image prune
docker system prune -a

# Restart
docker restart container-id
docker stop container-id
docker start container-id
```

---

## 4) GitHub (Web Platform)

### What is GitHub?
Web platform for hosting Git repositories and collaboration.

### Prerequisites
- Web browser
- GitHub account (free)
- Internet connection

### How to create GitHub account

1. Go to https://github.com
2. Click "Sign up"
3. Enter email, password, username
4. Verify email
5. Done

---

### Common GitHub Web Errors

#### Error 1: `Repository not found` (when cloning)

**When it happens:**
- Trying to clone private repo without permission
- Wrong repo URL
- Repo was deleted

**Log file:**
- Terminal output when running `git clone`

**How to identify:**
```bash
git remote -v
# Check URL

curl -I https://github.com/user/repo
# Check if repo exists
```

**Root cause:**
- Private repo, no access
- Wrong URL
- Repo deleted

**Fix:**
```bash
# Check correct URL
# Go to GitHub.com > repo > Code button > copy URL

git clone https://github.com/user/repo.git

# If private, add credentials
git clone https://user:token@github.com/user/private-repo.git
```

**Verify:**
```bash
cd repo
git status
```

---

#### Error 2: `Permission denied (publickey)` for SSH

**When it happens:**
- SSH key not set up
- Wrong SSH key
- Key not added to GitHub

**Log file:**
- Terminal output when running git commands

**How to identify:**
```bash
ssh -T git@github.com
# Should say: successfully authenticated

cat ~/.ssh/id_ed25519.pub
# Check if key exists
```

**Root cause:**
- No SSH key generated
- SSH key not added to GitHub
- Wrong SSH key used

**Fix:**
```bash
# 1. Generate SSH key
ssh-keygen -t ed25519 -C "your_email@example.com"
# Press Enter for default location
# Set passphrase (optional)

# 2. Show public key
cat ~/.ssh/id_ed25519.pub

# 3. Copy and add to GitHub
# GitHub.com > Settings > SSH and GPG keys > New SSH key
# Paste the key, give it a name, click Add

# 4. Test
ssh -T git@github.com
```

**Verify:**
```bash
ssh -T git@github.com
# Should say: successfully authenticated

git clone git@github.com:user/repo.git
# Should work
```

---

#### Error 3: `You do not have permission to push` (403)

**When it happens:**
- You are not a collaborator
- No write permission to repo
- Trying to push to someone else's repo

**Log file:**
- Terminal output when running `git push`

**How to identify:**
```bash
git remote -v
# Check which repo

# Go to GitHub.com and check your access
# Settings > Collaborators (for your repo)
# Or ask repo owner for access
```

**Root cause:**
- Not added as collaborator
- Read-only permission
- Wrong repository

**Fix:**
```bash
# If it's your repo, add collaborator
# GitHub.com > Settings > Collaborators > Add people

# If someone else's repo, fork it first
# GitHub.com > repo > Fork > Create fork

# Then push to your fork
git remote set-url origin https://github.com/YOUR_USERNAME/forked-repo.git
git push origin main
```

**Verify:**
```bash
git push origin main
# Should succeed

# Check on GitHub.com
# Repo should show your new commits
```

---

### Important GitHub Web Actions

```bash
# For HTTPS authentication
# Generate token: GitHub.com > Settings > Developer settings > Personal access tokens > Tokens (classic)
# Select: repo, read:org, workflow
# Copy token and use as password when prompted

# For SSH authentication
# Already covered above

# Check what you can access
git ls-remote https://github.com/user/repo.git
# If 403: no access
```

---

## 5) GitHub Actions (CI/CD)

### What is GitHub Actions?
Automated workflow platform within GitHub.

### Prerequisites
- GitHub account
- Repository
- .github/workflows/ folder in repo

### File location
```
repo/
├── .github/
│   └── workflows/
│       └── ci.yml
```

---

### Common GitHub Actions Errors

#### Error 1: `fatal: could not read Username for 'https://github.com'`

**When it happens:**
- Workflow trying to git push/pull
- No GITHUB_TOKEN provided
- Credentials not set

**Log file:**
- GitHub Actions workflow log
- Path: repo > Actions > workflow name > job > step

**How to identify:**
```yaml
# Check workflow YAML file
cat .github/workflows/ci.yml
# Look for: uses: actions/checkout@v3
```

**Root cause:**
- GITHUB_TOKEN not passed to checkout
- Git credentials not configured in workflow

**Fix:**
```yaml
name: CI

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
        with:
          token: ${{ secrets.GITHUB_TOKEN }}

      - name: Configure Git
        run: |
          git config --global user.email "action@github.com"
          git config --global user.name "GitHub Action"

      - name: Push
        run: |
          git push origin main
```

**Verify:**
```bash
# Commit and push workflow file
git add .github/workflows/ci.yml
git commit -m "Fix: Add GITHUB_TOKEN to workflow"
git push origin main

# Go to repo > Actions
# Workflow should now pass
```

---

#### Error 2: `Error: Process completed with exit code 1`

**When it happens:**
- Build script failed
- Test failed
- Command returned error

**Log file:**
- GitHub Actions workflow log (full step output)

**How to identify:**
```yaml
# Read the step that failed
# Look at output above this error

# Common causes:
# - npm install failed
# - npm test failed
# - build command failed
```

**Root cause:**
- Depends on the specific command
- Usually build/test/script error

**Fix:**
```bash
# Run same command locally
npm install
npm test
npm run build

# Find the actual error
# Fix locally
git commit -m "Fix: build error"
git push origin main

# Re-run workflow
```

**Verify:**
```bash
# Go to repo > Actions
# Workflow should pass
```

---

#### Error 3: `Resource not accessible by integration`

**When it happens:**
- Workflow missing required permissions
- GITHUB_TOKEN lacks necessary scopes

**Log file:**
- GitHub Actions workflow log

**How to identify:**
```yaml
# Check workflow permissions
cat .github/workflows/ci.yml
# Look for: permissions section

# Check repo settings
# Settings > Actions > General
# Look for: Permissions section
```

**Root cause:**
- Workflow trying to do something GITHUB_TOKEN cannot
- Example: create release without 'contents: write' permission

**Fix option 1 (Add workflow permissions):**
```yaml
name: Release

on: [release]

permissions:
  contents: write
  pull-requests: write

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - run: echo "Building..."
```

**Fix option 2 (Add repo permissions):**
```bash
# GitHub.com > repo > Settings > Actions > General
# Scroll to "Workflow permissions"
# Select: Read and write permissions
# Save
```

**Verify:**
```bash
# Re-run workflow
# Go to repo > Actions > workflow > Re-run jobs

# Should succeed
```

---

### Important GitHub Actions Concepts

```yaml
# Workflow structure
name: Workflow Name

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  job-name:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - run: npm install
      - run: npm test

# Environment
env:
  NODE_ENV: production

# Secrets (set in repo settings)
# Use: ${{ secrets.SECRET_NAME }}

# Files and outputs
# Artifacts: uses: actions/upload-artifact@v3
# Caching: uses: actions/cache@v3
```

---

## Summary: Tool-Wise Troubleshooting

| Tool | Common Issue | Quick Check | Quick Fix |
|------|------|------|------|
| **Git** | not a git repo | `pwd` + `ls -la` | `git init` or `git clone` |
| **Git** | auth failed | `ssh -T git@github.com` | generate SSH key or PAT |
| **Git** | cannot push | `git status` | `git pull --rebase` then push |
| **Node/npm** | command not found | `node --version` | install Node.js |
| **npm** | package not found | `npm search pkg-name` | check correct package name |
| **npm** | permission denied | `ls -l /usr/local/lib/node_modules` | fix npm permissions |
| **Docker** | command not found | `which docker` | install Docker, add to group |
| **Docker** | port in use | `lsof -i :8080` | use different port or stop other container |
| **Docker** | no space | `docker system df` | `docker system prune -a` |
| **GitHub** | repo not found | `git remote -v` + check GitHub.com | verify URL and access |
| **GitHub** | SSH key issue | `ssh -T git@github.com` | generate and add SSH key |
| **GitHub Actions** | token error | check workflow YAML | add `token: ${{ secrets.GITHUB_TOKEN }}` |

---

## How to Use This Guide

1. **Identify your tool** (Git, npm, Docker, GitHub, GitHub Actions)
2. **Find the error section**
3. **Follow: Identify > Root Cause > Fix > Verify**
4. **Check logs** using the specified log file location
5. **Run suggested commands** step by step
6. **Verify** the fix worked

This is professional troubleshooting.
