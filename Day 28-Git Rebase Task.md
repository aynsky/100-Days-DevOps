# Git Rebase Task – Nautilus App Repository

## 📌 Scenario Overview

The Nautilus application development team maintains a Git repository with the following setup:

- **Repository Path:** `/opt/demo.git`
- **Cloned Location:** `/usr/src/kodekloudrepos` on the Storage Server in Stratos DC
- **Current Situation:** Developer working on feature branch while new changes exist in master

### Developer Requirements

The developer needs to:

1. **Rebase the feature branch** with the master branch to include latest changes
2. **Avoid merge commits** (no simple `git merge master` approach)
3. **Preserve all feature branch work** (ensure no data loss)
4. **Push the updated feature branch** back to remote repository

---

## 🎯 Why Rebase Instead of Merge?

| Approach | Result | History Impact |
|----------|--------|----------------|
| `git merge master` | Creates a **merge commit** | Cluttered history with merge commits |
| `git rebase master` | **Rewrites branch history** | Clean, linear history as if feature was built on latest master |

### Benefits of Rebasing
- ✅ **Cleaner commit history** - no unnecessary merge commits
- ✅ **Linear timeline** - easier to understand project evolution
- ✅ **Preserves feature work** - all commits remain intact
- ✅ **Professional workflow** - industry best practice for feature branches

---

## ⚡ Step-by-Step Rebase Process

### Prerequisites
- Ensure you have appropriate permissions on the Storage Server
- Verify network connectivity to remote repository
- Have conflict resolution strategy ready if needed

### 1. Navigate to Repository
```bash
cd /usr/src/kodekloudrepos
```

### 2. Update Master Branch
Ensure you have the latest changes from remote master:
```bash
git checkout master
git pull origin master
```

### 3. Switch to Feature Branch
```bash
git checkout feature
```
> **Note:** Replace `feature` with your actual feature branch name

### 4. Perform the Rebase
```bash
git rebase master
```

#### 🔧 Handling Conflicts (if they occur)
If Git encounters conflicts during rebase:

1. **Identify conflicted files:**
   ```bash
   git status
   ```

2. **Resolve conflicts manually:**
   - Open conflicted files in your editor
   - Look for conflict markers (`<<<<<<<`, `=======`, `>>>>>>>`)
   - Resolve conflicts by choosing or combining changes
   - Remove conflict markers

3. **Stage resolved files:**
   ```bash
   git add <resolved-filename>
   ```

4. **Continue rebase:**
   ```bash
   git rebase --continue
   ```

5. **Repeat until complete** - process may require multiple iterations

#### Alternative Rebase Commands
```bash
# If you want to abort the rebase
git rebase --abort

# If you want to skip a problematic commit
git rebase --skip
```

### 5. Push Updated Branch
Since rebase rewrites commit history, force push is required:

```bash
git push origin feature --force-with-lease
```

> **⚠️ Important:** Use `--force-with-lease` instead of `--force` for safer force pushing. It prevents overwriting changes if someone else pushed to the branch.

---

## ✅ Verification Steps

After completing the rebase:

1. **Check branch status:**
   ```bash
   git status
   git log --oneline -10
   ```

2. **Verify remote sync:**
   ```bash
   git log --oneline origin/feature -5
   ```

3. **Confirm no data loss:**
   - Review your feature commits are still present
   - Test application functionality if applicable

---

## 🎯 Expected Final Outcome

- ✅ Feature branch contains all latest master changes
- ✅ Feature branch commits appear to be built on top of latest master
- ✅ No merge commits introduced in history
- ✅ All developer work preserved and functional
- ✅ Remote repository updated with rebased feature branch
- ✅ Clean, linear Git history maintained

---

## 🚨 Best Practices & Warnings

### ⚠️ When NOT to Rebase
- **Never rebase shared/public branches** that other developers are using
- **Avoid rebasing** if multiple developers are collaborating on the same feature branch
- **Don't rebase** commits that have already been pushed and merged to master

### ✅ Best Practices
- Always `git pull` master before rebasing
- Use `--force-with-lease` instead of `--force` when pushing
- Communicate with team when rebasing shared feature branches
- Keep feature branches small and focused for easier rebasing
- Test thoroughly after rebasing before pushing

---

## 🔧 Troubleshooting

### Common Issues

| Issue | Solution |
|-------|----------|
| `fatal: Not a git repository` | Navigate to correct directory (`cd /usr/src/kodekloudrepos`) |
| `error: failed to push` | Use `--force-with-lease` flag |
| `CONFLICT: Merge conflict` | Resolve conflicts manually, stage files, continue rebase |
| `Your branch is ahead by N commits` | Normal after rebase; push with `--force-with-lease` |

### Recovery Commands
```bash
# View reflog to see recent changes
git reflog

# Reset to previous state if needed
git reset --hard HEAD@{N}  # where N is reflog entry number
```

---

*This document serves as a comprehensive guide for performing Git rebase operations in the Nautilus application development workflow.*