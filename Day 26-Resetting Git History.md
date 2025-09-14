# Day 26 - Resetting Git History

**Date:** 2025-09-14  
**Topic:** Cleaning repository history and managing commit timelines

---

## 📖 Summary

Practiced cleaning a repository by resetting branch history to a specific commit, keeping only essential commits. This technique is useful for test repositories, removing unwanted commits, or cleaning up messy development history before sharing code.

---

## 💻 Commands & Usage

### Viewing History Before Reset

```bash
# Check commit history with details
git log --oneline

# View detailed commit information
git log --oneline --graph --decorate

# See what files changed in recent commits
git log --stat --oneline -5

# Check remote vs local history
git log --oneline origin/master..HEAD
```

### Types of Git Reset

#### 1. Soft Reset (Keeps Changes Staged)
```bash
# Move HEAD but keep changes in staging area
git reset --soft <commit_hash>

# Example: Undo last commit but keep changes staged
git reset --soft HEAD~1
```

#### 2. Mixed Reset (Default - Unstages Changes)
```bash
# Move HEAD and unstage changes (but keep in working directory)
git reset --mixed <commit_hash>

# Same as above (--mixed is default)
git reset <commit_hash>
```

#### 3. Hard Reset (Destroys All Changes)
```bash
# Move HEAD and discard ALL changes (DANGEROUS!)
git reset --hard <commit_hash>

# Reset to specific commit (your example)
git reset --hard a1b2c3d

# Reset to previous commit
git reset --hard HEAD~1
```

### Force Pushing Changes

```bash
# Force push to overwrite remote history (DANGEROUS!)
git push origin master --force

# Safer alternative - only push if no one else has pushed
git push origin master --force-with-lease

# Push to different branch for safety
git push origin master:backup-branch --force
```

### Verification Commands

```bash
# Verify remaining commits
git log --oneline

# Check working directory status
git status

# Compare with remote
git log --oneline origin/master
```

---

## ⚠️ DANGER ZONE - Critical Safety Information

### 🚨 Before Using `git reset --hard`:

- **BACKUP YOUR WORK** - Create a branch or tag first
- **Check you're on the right branch** - `git branch` to confirm
- **Verify the commit hash** - Double and triple check!
- **Communicate with team** - If working with others
- **Consider alternatives** - Often `git revert` is safer

### Pre-Reset Safety Checklist:
```bash
# 1. Create a backup branch
git branch backup-before-reset

# 2. Confirm current branch
git branch

# 3. Verify target commit
git show <commit_hash>

# 4. Check what will be lost
git log --oneline <commit_hash>..HEAD

# 5. Only then proceed with reset
git reset --hard <commit_hash>
```

---

## 🔧 Complete Workflow Examples

### Example 1: Cleaning Test Repository
```bash
# Check current history
git log --oneline
# Output: 
# f4e5d6c Add unnecessary test file
# c3b2a1d Fix typo in readme
# b1a2c3d Add data.txt file
# a1b2c3d Initial commit

# Reset to keep only essential commits
git reset --hard b1a2c3d

# Force push to remote
git push origin master --force

# Verify clean history
git log --oneline
# Output:
# b1a2c3d Add data.txt file  
# a1b2c3d Initial commit
```

### Example 2: Undoing Multiple Commits Safely
```bash
# Create safety backup first
git tag before-cleanup HEAD

# Reset to desired state
git reset --hard HEAD~3

# If you made a mistake, recover:
git reset --hard before-cleanup
```

### Example 3: Interactive History Cleanup
```bash
# For more control, use interactive rebase instead
git rebase -i HEAD~5

# This opens editor to choose which commits to keep/modify
# Safer than hard reset for complex history changes
```

---

## 🎯 Key Takeaways

### Core Concepts
- **`git reset --hard <commit_hash>`** moves HEAD and branch pointer, discarding later commits
- **Always verify commit hash** to avoid deleting important history
- **Force push (`--force`)** updates remote with rewritten history
- **History rewriting affects everyone** who has cloned the repository

### Reset Types Summary
| Type | HEAD | Index (Staging) | Working Directory |
|------|------|-----------------|-------------------|
| `--soft` | ✅ Moves | ❌ Unchanged | ❌ Unchanged |
| `--mixed` | ✅ Moves | ✅ Reset | ❌ Unchanged |
| `--hard` | ✅ Moves | ✅ Reset | ✅ Reset |

### When to Use Each Reset Type
- **`--soft`**: Want to recommit changes differently
- **`--mixed`**: Want to restage changes selectively  
- **`--hard`**: Want to completely discard changes (CAREFUL!)

---

## 🛡️ Safer Alternatives to Consider

### 1. Git Revert (Recommended for Shared Repositories)
```bash
# Create new commit that undoes previous commit
git revert <commit_hash>

# Revert multiple commits
git revert <commit1>..<commit2>
```

### 2. Create New Branch Instead
```bash
# Start fresh branch from desired commit
git checkout -b clean-history <commit_hash>
```

### 3. Interactive Rebase for Complex Changes
```bash
# More control over history modification
git rebase -i HEAD~5
```

### 4. Squash Commits During Merge
```bash
# Clean up feature branch when merging
git merge --squash feature-branch
```

---

## 🔍 Recovery Techniques

### If You Made a Mistake:

#### 1. Use Git Reflog (Short-term Recovery)
```bash
# See recent HEAD movements
git reflog

# Reset to previous state
git reset --hard HEAD@{2}
```

#### 2. Check for Backup Tags/Branches
```bash
# List all branches and tags
git branch -a
git tag

# Reset to backup if available
git reset --hard backup-tag
```

#### 3. Recovery from Remote
```bash
# If remote still has the commits
git fetch origin
git reset --hard origin/master
```

---

## 🚨 Team Collaboration Warnings

### Before Force Pushing:
- **Communicate with team** - Let everyone know about history changes
- **Coordinate timing** - Ensure no one is actively working
- **Provide migration instructions** - Help teammates update their local repos

### After Force Push, Team Members Need:
```bash
# Teammates must reset their local branches
git fetch origin
git reset --hard origin/master

# Or start fresh
git clone <repository_url>
```

---

## 💡 Pro Tips

### 1. Use Commit Ranges
```bash
# See what commits will be removed
git log --oneline <target_commit>..HEAD

# Count commits that will be lost
git rev-list --count <target_commit>..HEAD
```

### 2. Partial File Reset
```bash
# Reset specific file to previous version
git checkout <commit_hash> -- filename.txt
```

### 3. Reset with Confirmation
```bash
# Create alias for safer reset
git config --global alias.safe-reset '!f() { git reset --hard $1 && git log --oneline -10; }; f'
```

---

## 📝 Practice Scenarios

1. **Scenario 1:** You committed sensitive data and need to remove it from history
2. **Scenario 2:** Your feature branch has messy commits that need cleaning before merge
3. **Scenario 3:** You accidentally committed to the wrong branch
4. **Scenario 4:** Test repository has become cluttered with experimental commits

---

## 🔗 Related Commands

- `git log` - View commit history
- `git reflog` - View reference log (for recovery)
- `git revert` - Safer alternative to reset
- `git rebase` - Interactive history modification
- `git checkout` - Switch branches or restore files

---

## 📚 Additional Resources

- [Git Reset Documentation](https://git-scm.com/docs/git-reset)
- [Understanding Git Reset](https://www.atlassian.com/git/tutorials/undoing-changes/git-reset)
- [Git Reflog for Recovery](https://git-scm.com/docs/git-reflog)

---

*Remember: With great power comes great responsibility. Git reset is powerful but dangerous - always have a backup plan! 🚨*