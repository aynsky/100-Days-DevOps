# Day 25 - Git Stash & Restoring Changes

**Date:** 2025-09-14  
**Topic:** Temporary storage and restoration of uncommitted changes

---

## 📖 Summary

Learned how to temporarily save uncommitted changes in Git using `git stash` and restore them later using `git stash apply` or `git stash pop`. This is essential when you need to switch branches or handle interruptions without committing incomplete changes.

---

## 💻 Commands & Usage

### Basic Stashing Operations

```bash
# Check current uncommitted changes
git status

# Stash all uncommitted changes (tracked files only)
git stash

# Stash with a descriptive message
git stash push -m "Working on user authentication feature"

# Stash including untracked files
git stash push -u -m "Include new config files"
```

### Managing Multiple Stashes

```bash
# List all stashes with details
git stash list

# View specific stash contents
git stash show stash@{0}

# View detailed diff of a stash
git stash show -p stash@{1}
```

### Restoring Stashed Changes

```bash
# Apply the most recent stash (keeps stash in list)
git stash apply

# Apply a specific stash by index
git stash apply stash@{1}

# Apply and immediately remove stash from list
git stash pop

# Apply specific stash and remove it
git stash pop stash@{2}
```

### Cleaning Up Stashes

```bash
# Remove a specific stash
git stash drop stash@{1}

# Clear all stashes (be careful!)
git stash clear

# Create a branch from a stash
git stash branch new-feature-branch stash@{0}
```

### Complete Workflow Example

```bash
# 1. Check what you're working on
git status

# 2. Stash your current work
git stash push -m "Half-finished login validation"

# 3. Switch to another branch or pull updates
git checkout main
git pull origin main

# 4. Return to your work
git checkout feature-branch

# 5. Restore your stashed changes
git stash pop

# 6. Continue working and eventually commit
git add .
git commit -m "Complete login validation feature"
git push origin feature-branch
```

---

## 🎯 Key Takeaways

### Core Concepts
- **`git stash`** is like a "temporary drawer" for uncommitted work
- **`apply`** restores changes but keeps the stash in the list
- **`pop`** restores changes and removes the stash from the list
- **Stashing helps manage interruptions** without committing unfinished code

### When to Use Git Stash
- 🔄 **Branch switching** - Need to switch branches but current work isn't ready to commit
- 🚨 **Urgent fixes** - Drop everything to work on a hotfix
- 📥 **Pulling updates** - Need clean working directory to pull changes
- 🧪 **Experimenting** - Try something quickly without losing current work
- 🔧 **Resolving conflicts** - Temporarily store changes during complex merges

### Best Practices
- ✅ **Use descriptive messages** with `git stash push -m "message"`
- ✅ **Include untracked files** when needed with `-u` flag
- ✅ **Regularly clean up** old stashes with `git stash drop`
- ✅ **Use `git stash show`** to review before applying
- ⚠️ **Be careful with `git stash clear`** - it removes ALL stashes

---

## 🔍 Advanced Tips

### Partial Stashing
```bash
# Stash only specific files
git stash push -m "Only CSS changes" -- styles.css

# Interactive stashing (choose what to stash)
git stash push -p
```

### Stash and Branch Creation
```bash
# Create new branch from stash (useful for experimental work)
git stash branch experimental-feature stash@{0}
```

### Viewing Stash History
```bash
# See what files are in a stash
git stash show --name-only stash@{1}

# Compare stash with current working directory
git stash show -p
```

---

## 🚨 Common Pitfalls to Avoid

- **Forgetting about stashes** - Use `git stash list` regularly
- **Stashing untracked files** - Remember to use `-u` flag when needed
- **Applying wrong stash** - Always check `git stash list` first
- **Not using descriptive messages** - Makes finding the right stash difficult
- **Letting stashes accumulate** - Clean up old stashes regularly

---

## 📝 Practice Scenarios

Try these scenarios to reinforce your learning:

1. **Scenario 1:** You're working on a feature but need to quickly fix a bug on main branch
2. **Scenario 2:** You have uncommitted changes but need to pull the latest updates
3. **Scenario 3:** You want to experiment with different approaches to the same problem
4. **Scenario 4:** You accidentally started working on the wrong branch

---

## 🔗 Related Commands

- `git status` - Check working directory state
- `git checkout` - Switch branches
- `git add` - Stage changes
- `git commit` - Save changes permanently
- `git reset` - Undo changes (alternative to stash)

---

*Git stash is one of those commands that once you learn it, you'll wonder how you ever worked without it! 🚀*