---
description: Set up thoughts directory structure for documentation and notes
---

# Thoughts Setup

This command sets up the thoughts directory structure for storing research, plans, handoffs, and personal notes that persist across Claude Code sessions.

## Structure Overview

The thoughts system uses a central `~/thoughts/` directory with symlinks in each repository:

```
~/thoughts/
├── repos/
│   └── {repo-name}/           # Per-repository thoughts
│       ├── {user}/            # Your personal notes
│       │   ├── tickets/
│       │   └── notes/
│       └── shared/            # Team-shared documents
│           ├── research/      # Research documents
│           ├── plans/         # Implementation plans
│           ├── handoffs/      # Session handoff documents
│           ├── prs/           # PR descriptions
│           ├── tickets/       # Ticket documentation
│           └── issues/        # Issue files
└── global/
    ├── {user}/                # Cross-repo personal notes
    └── shared/                # Cross-repo shared notes
```

Each repository has a `thoughts/` directory with symlinks:
```
{repo}/thoughts/
├── {user}/    → ~/thoughts/repos/{repo-name}/{user}/
├── shared/    → ~/thoughts/repos/{repo-name}/shared/
└── global/    → ~/thoughts/global/
```

## Setup Process

When this command is invoked, perform these steps:

### Step 1: Gather Information

```bash
# Get current user and repo name
USER_NAME=$(whoami)
REPO_NAME=$(basename $(git rev-parse --show-toplevel))
REPO_ROOT=$(git rev-parse --show-toplevel)
```

### Step 2: Create Home Directory Structure

Create the central thoughts directory if it doesn't exist:

```bash
# Create repo-specific directories
mkdir -p ~/thoughts/repos/${REPO_NAME}/${USER_NAME}/tickets
mkdir -p ~/thoughts/repos/${REPO_NAME}/${USER_NAME}/notes
mkdir -p ~/thoughts/repos/${REPO_NAME}/shared/research
mkdir -p ~/thoughts/repos/${REPO_NAME}/shared/plans
mkdir -p ~/thoughts/repos/${REPO_NAME}/shared/handoffs
mkdir -p ~/thoughts/repos/${REPO_NAME}/shared/prs
mkdir -p ~/thoughts/repos/${REPO_NAME}/shared/tickets
mkdir -p ~/thoughts/repos/${REPO_NAME}/shared/issues

# Create global directories
mkdir -p ~/thoughts/global/${USER_NAME}
mkdir -p ~/thoughts/global/shared
```

### Step 3: Create Repository Symlinks

Create the thoughts directory in the repo with symlinks:

```bash
cd ${REPO_ROOT}
mkdir -p thoughts

# Create symlinks (use -sfn to overwrite existing)
ln -sfn ~/thoughts/repos/${REPO_NAME}/${USER_NAME} thoughts/${USER_NAME}
ln -sfn ~/thoughts/repos/${REPO_NAME}/shared thoughts/shared
ln -sfn ~/thoughts/global thoughts/global

# Exclude from git
echo '*' > thoughts/.gitignore
```

### Step 4: Verify Setup

```bash
ls -la thoughts/
```

You should see:
```
thoughts/
├── {user}  -> ~/thoughts/repos/{repo}/...
├── shared  -> ~/thoughts/repos/{repo}/shared
├── global  -> ~/thoughts/global
└── .gitignore
```

### Step 5: Add to Repository .gitignore (Optional)

If you want to ensure thoughts is never committed:

```bash
echo 'thoughts/' >> .gitignore
```

## Usage After Setup

Once set up, use these paths in commands:
- `thoughts/shared/research/` - Research documents
- `thoughts/shared/plans/` - Implementation plans
- `thoughts/shared/handoffs/` - Session handoffs
- `thoughts/{user}/notes/` - Personal notes (where {user} is your username)

## Multi-User Support

When collaborating, each user runs this setup with their own username. The shared directories are... shared, while personal directories are per-user:

```
thoughts/
├── alice/   → ~/thoughts/repos/{repo}/alice/
├── bob/     → ~/thoughts/repos/{repo}/bob/
├── shared/  → ~/thoughts/repos/{repo}/shared/
└── global/  → ~/thoughts/global/
```

## Troubleshooting

**Symlinks not working**: Ensure the target directories exist in `~/thoughts/`

**Permission issues**: Check that you own `~/thoughts/` and its contents

**Thoughts appearing in git status**: Add `thoughts/` to `.gitignore` or ensure `thoughts/.gitignore` contains `*`
