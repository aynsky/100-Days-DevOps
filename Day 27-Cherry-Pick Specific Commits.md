# Day 27 - Cherry-Pick Specific Commits

**Date:** 2025-09-14  
**Topic:** Selective commit integration across branches

---

## 📖 Summary

Learned how to selectively bring a commit from one branch into another using `git cherry-pick`. This is invaluable when work on a feature branch is ongoing but you need a specific commit on `master`, or when you want to apply a hotfix to multiple branches without merging everything.

---

## 💻 Commands & Usage

### Basic Cherry-Pick Workflow

```bash
# 1. Switch to target branch (where you want the commit)
git checkout master

# 2. Identify commits available in source branch
git log feature --oneline

# 3. Cherry-pick specific commit
git cherry-pick <commit_hash>

# 4. Push changes to remote
git push origin master
```

### Advanced Cherry-Pick Operations

#### Cherry-Pick Multiple Commits
```bash
# Pick multiple specific commits
git cherry-pick <commit1> <commit2> <commit3>

# Pick a range of commits (excluding start, including end)
git cherry-pick <start_commit>..<end_commit>

# Pick a range including start commit
git cherry-pick <start_commit>^..<end_commit>
```

#### Cherry-Pick with Options
```bash
# Cherry-pick without auto-commit (review changes first)
git cherry-pick --no-commit <commit_hash>
git status  # Review changes
git commit  # Commit when ready

# Cherry-pick and edit commit message
git cherry-pick --edit <commit_hash>

# Cherry-pick only the changes, not commit info
git cherry-pick --no-commit <commit_hash>
git reset  # Unstage to modify before committing
```

#### Handling Conflicts During Cherry-Pick
```bash
# If conflicts occur during cherry-pick
git status                    # See conflicted files
# Edit files to resolve conflicts
git add <resolved_files>      # Stage resolved files
git cherry-pick --continue   # Complete the cherry-pick

# Or abort if too complex
git cherry-pick --abort       # Cancel and return to original state
```

---

## 🎯 Real-World Use Cases

### Use Case 1: Hotfix Distribution
```bash
# Scenario: Critical bug fix needs to go to multiple branches

# 1. Create hotfix on dedicated branch
git checkout -b hotfix-security-patch
# ... make fix and commit
git commit -m "Fix: Security vulnerability in user auth"

# 2. Apply to main branch
git checkout main
git cherry-pick hotfix-security-patch

# 3. Apply to release branches
git checkout release-v2.1
git cherry-pick hotfix-security-patch

git checkout release-v2.0  
git cherry-pick hotfix-security-patch
```

### Use Case 2: Selective Feature Integration
```bash
# Scenario: Feature branch has 5 commits, but only 2 are ready for production

# Check what's in feature branch
git log feature-user-profiles --oneline
# Output:
# f4e5d6c Add profile validation (ready)
# c3b2a1d Update user model (ready)  
# b1a2c3d Add profile photos (not ready)
# a1b2c3d Refactor auth system (not ready)
# 9z8y7x6 Fix profile bug (ready)

# Cherry-pick only ready commits
git checkout main
git cherry-pick 9z8y7x6  # Fix profile bug
git cherry-pick c3b2a1d  # Update user model  
git cherry-pick f4e5d6c  # Add profile validation
```

### Use Case 3: Cross-Branch Bug Fix
```bash
# Bug discovered in feature branch but fix needed immediately in main

# 1. On feature branch, commit the fix
git checkout feature-payment-system
git add bugfix.js
git commit -m "Fix: Payment validation error"

# 2. Apply same fix to main branch
git checkout main
git cherry-pick feature-payment-system  # Gets the latest commit

# 3. Continue working on feature
git checkout feature-payment-system
# Feature development continues...
```

---

## 🔍 Identifying Commits to Cherry-Pick

### Viewing Available Commits
```bash
# See commits in source branch not in current branch
git log main..feature --oneline

# Show commits with file changes
git log feature --oneline --stat

# See commits with specific author
git log feature --oneline --author="John Doe"

# Find commits by message content
git log feature --oneline --grep="fix"

# Show commit details before picking
git show <commit_hash>
```

### Visual Branch Comparison
```bash
# Compare branches visually
git log --oneline --graph --all

# See divergent commits between branches  
git log --oneline --left-right main...feature
```

---

## ⚠️ Cherry-Pick Considerations & Best Practices

### When to Use Cherry-Pick
✅ **Good Use Cases:**
- **Hotfixes** that need to go to multiple branches
- **Selective integration** from unfinished features
- **Bug fixes** discovered in feature branches
- **Configuration updates** needed across branches
- **Documentation fixes** that apply everywhere

❌ **Avoid Cherry-Pick When:**
- **Merging entire features** (use `git merge` instead)
- **Commits have complex dependencies** on other commits
- **Large refactoring changes** that affect many files
- **Sequential commits** that build on each other

### Best Practices
```bash
# 1. Always check commit dependencies first
git log --graph <commit_hash>~5..<commit_hash>

# 2. Test cherry-picked changes
# Run tests after cherry-picking to ensure compatibility

# 3. Use descriptive commit messages that reference original
git cherry-pick --edit <commit_hash>
# Edit message to include: "Cherry-picked from branch feature-xyz"

# 4. Keep track of what you've cherry-picked
git log --oneline --grep="cherry.*pick"
```

---

## 🚨 Common Pitfalls & Solutions

### Problem 1: Merge Conflicts
```bash
# When conflicts occur
<<<<<<< HEAD
current branch code
=======
cherry-picked code  
>>>>>>> <commit_hash>

# Solution: Resolve manually, then continue
git add <resolved_files>
git cherry-pick --continue
```

### Problem 2: Duplicate Commits
```bash
# Avoid cherry-picking commits that will be merged later
# This creates duplicate commits with different hashes

# Check if commit already exists
git log --grep="commit message" --oneline
```

### Problem 3: Missing Dependencies
```bash
# Cherry-picked commit might depend on previous commits

# Solution: Cherry-pick dependencies first
git cherry-pick <dependency_commit> <target_commit>

# Or cherry-pick a range
git cherry-pick <start_commit>^..<end_commit>
```

---

## 🔧 Advanced Cherry-Pick Techniques

### Cherry-Pick from Remote Branches
```bash
# Fetch latest from remote
git fetch origin

# Cherry-pick from remote branch
git cherry-pick origin/feature-branch

# Cherry-pick specific commit from remote
git cherry-pick a1b2c3d  # Works if you have the commit locally
```

### Interactive Cherry-Pick Selection
```bash
# Use interactive rebase to cherry-pick with modifications
git rebase -i --onto main <start_commit> <end_commit>

# This opens editor where you can:
# - pick: use commit as-is
# - edit: modify commit  
# - squash: combine with previous
# - drop: skip this commit
```

### Cherry-Pick and Squash
```bash
# Cherry-pick multiple commits as one
git cherry-pick --no-commit <commit1>
git cherry-pick --no-commit <commit2>
git cherry-pick --no-commit <commit3>

# Then create single commit
git commit -m "Combined feature updates from development branch"
```

---

## 📊 Cherry-Pick vs Other Git Operations

| Operation | Purpose | Use When | Result |
|-----------|---------|----------|---------|
| `cherry-pick` | Apply specific commit | Need one commit from branch | Single commit copied |
| `merge` | Combine branch history | Want entire branch | All commits included |
| `rebase` | Replay commits | Want to update branch base | History rewritten |
| `reset` | Move branch pointer | Want to undo commits | History modified |

---

## 🎯 Key Takeaways

### Core Concepts
- **`git cherry-pick <commit_hash>`** allows selective merging of individual commits
- **Does not merge entire branch** - only applies chosen commits
- **Helps maintain stability** in main branch while selectively integrating updates
- **Creates new commit** with same changes but different hash

### Strategic Benefits
- **Risk Management**: Apply only tested/verified commits to production
- **Feature Control**: Release features incrementally  
- **Hotfix Distribution**: Apply critical fixes to multiple branches quickly
- **Development Flexibility**: Continue feature work while releasing parts early

### Remember
- Test after cherry-picking to ensure compatibility
- Be aware of commit dependencies  
- Document cherry-picked commits for team awareness
- Consider merge conflicts and resolution strategies

---

## 📝 Practice Scenarios

1. **Scenario 1:** A critical security fix is developed in a feature branch but needs immediate deployment
2. **Scenario 2:** Large feature is 80% complete, but one small improvement is ready for release
3. **Scenario 3:** Bug fix committed to wrong branch needs to be applied to correct branch
4. **Scenario 4:** Documentation update made in development branch needs to be in main branch immediately

---

## 🔗 Related Commands

- `git log` - View commit history and identify commits to pick
- `git show` - Examine specific commit details
- `git merge` - Alternative for complete branch integration
- `git rebase` - Alternative for moving/combining commits
- `git format-patch` - Create patches for commits (alternative approach)

---

## 📚 Additional Resources

- [Git Cherry-Pick Documentation](https://git-scm.com/docs/git-cherry-pick)
- [Cherry-Pick vs Merge vs Rebase](https://www.atlassian.com/git/tutorials/cherry-pick)
- [Resolving Cherry-Pick Conflicts](https://git-scm.com/docs/git-cherry-pick#_examples)

---

*Cherry-pick is like being a selective gardener - you choose exactly which flowers (commits) to transplant to your garden (branch)! 🌸*