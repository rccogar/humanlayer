---
description: Set up worktree for reviewing colleague's branch
---

# Local Review

You are tasked with setting up a local review environment for a colleague's branch. This involves creating a worktree, setting up dependencies, and preparing for review.

## Process

When invoked with a parameter like `gh_username:branchName`:

1. **Parse the input**:
   - Extract GitHub username and branch name from the format `username:branchname`
   - If no parameter provided, ask for it in the format: `gh_username:branchName`

2. **Extract issue information**:
   - Look for issue numbers in the branch name (e.g., `gh-123`, `GH-123`, or just `123`)
   - Use this to create a short worktree directory name
   - If no issue found, use a sanitized version of the branch name

3. **Determine repository info**:
   - Get the current repo name: `basename $(git rev-parse --show-toplevel)`
   - Get the repo owner: `gh repo view --json owner -q .owner.login`

4. **Set up the remote and worktree**:
   - Check if the remote already exists using `git remote -v`
   - If not, add it: `git remote add USERNAME git@github.com:USERNAME/REPO_NAME`
   - Fetch from the remote: `git fetch USERNAME`
   - Create worktree: `git worktree add -b BRANCHNAME ~/wt/REPO_NAME/SHORT_NAME USERNAME/BRANCHNAME`

5. **Configure the worktree**:
   - Copy Claude settings if they exist: `cp .claude/settings.local.json WORKTREE/.claude/` (if file exists)
   - Run setup if Makefile exists: `make -C WORKTREE setup`
   - Initialize thoughts symlink if using thoughts convention: `ln -s ~/thoughts WORKTREE/thoughts`

## Error Handling

- If worktree already exists, inform the user they need to remove it first
- If remote fetch fails, check if the username/repo exists
- If setup fails, provide the error but continue with the launch

## Example Usage

```
/local_review colleague:feature/gh-123-new-feature
```

This will:
- Add 'colleague' as a remote (if not already present)
- Create worktree at `~/wt/{repo-name}/gh-123`
- Set up the environment
