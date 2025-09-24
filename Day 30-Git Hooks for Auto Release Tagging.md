# Day 30 – Git Hooks for Auto Release Tagging

## 📌 Problem Statement

The Nautilus application development team was working on a Git repository `/opt/official.git`, which was cloned under `/usr/src/kodekloudrepos` on the Storage server in Stratos DC.

They wanted to automatically create a **release tag** whenever changes were pushed to the `master` branch. The tag format should be:

```
release-YYYY-MM-DD
```

**Example:** On 2025-09-24 the tag should be `release-2025-09-24`.

---

## 🛠️ Implementation Steps

### 1. Create `post-update` Hook

Inside the bare repo `/opt/official.git/hooks/`, we added a `post-update` hook:

```bash
#!/usr/bin/sh

DATE=$(date +%F)        # YYYY-MM-DD
GIT_DIR=$(pwd)

echo "Creating tag release-$DATE"
git --git-dir="$GIT_DIR" tag "release-$DATE" master
```

Then made it executable:

```bash
chmod +x /opt/official.git/hooks/post-update
```

### 2. Merge Feature Branch and Push

In the working repo `/usr/src/kodekloudrepos/media`:

```bash
cd /usr/src/kodekloudrepos/media
git checkout master
git merge feature
git push
```

This push triggered the `post-update` hook in `/opt/official.git` and created the release tag.

---

## 🔍 Verification

### List all tags in the bare repo:
```bash
cd /opt/official.git
git tag
```

### Check today's tag:
```bash
git show release-$(date +%F)
```

### Verify tag details:
```bash
# Show tag information
git show-ref --tags

# Check specific tag commit
git rev-parse release-$(date +%F)

# View tag creation log
git log --oneline --decorate
```

---

## ⚠️ Issues Faced & Solutions

### 1. **Tag Not Created Initially**
- **Problem:** Used `git tag ... master` directly in the bare repo
- **Issue:** This failed because `master` was not properly referenced
- **Solution:** Fixed by pointing to the branch correctly in the hook script

### 2. **Testing Without Working Tree**
- **Problem:** The bare repo has no working tree
- **Issue:** Couldn't run merge operations inside `/opt/official.git`
- **Solution:** Had to push from the cloned repo (`/usr/src/kodekloudrepos`) instead

### 3. **Duplicate Tags on Same Day**
- **Problem:** If multiple pushes happen on the same day, the hook tries to re-create the same tag
- **Issue:** This causes an error when tag already exists
- **Future Improvement:** May add checking if the tag already exists before creation

---

## 📋 Understanding Git Hooks

### What is `post-update` Hook?
- **Trigger:** Runs after refs are updated by `git push`
- **Location:** `<bare-repo>/hooks/post-update`
- **Purpose:** Server-side hook for post-push operations
- **Execution:** Runs on the Git server, not the client

### Hook Lifecycle
```
Client Push → Server Receives → post-update Hook Executes → Tag Created
```

### Key Components of Our Hook
| Component | Purpose |
|-----------|---------|
| `DATE=$(date +%F)` | Gets current date in YYYY-MM-DD format |
| `GIT_DIR=$(pwd)` | Sets the Git directory context |
| `git --git-dir="$GIT_DIR"` | Executes Git commands in bare repo context |
| `tag "release-$DATE" master` | Creates annotated tag pointing to master |

---

## 🚀 Advanced Enhancements

### Enhanced Hook Script (Optional)
For future improvements, consider this enhanced version:

```bash
#!/usr/bin/sh

DATE=$(date +%F)
TAG_NAME="release-$DATE"
GIT_DIR=$(pwd)

# Check if tag already exists
if git --git-dir="$GIT_DIR" tag -l | grep -q "^$TAG_NAME$"; then
    echo "Tag $TAG_NAME already exists, skipping creation"
    exit 0
fi

echo "Creating tag $TAG_NAME"
git --git-dir="$GIT_DIR" tag "$TAG_NAME" master

echo "Tag $TAG_NAME created successfully"
```

### Additional Verification Commands
```bash
# List all release tags
git tag -l "release-*"

# Sort tags by date
git tag -l "release-*" | sort

# Show latest release tag
git tag -l "release-*" | sort | tail -1

# Get commit hash for specific tag
git rev-list -n 1 release-$(date +%F)
```

---

## 📊 Repository Structure

```
/opt/official.git/                 # Bare repository
├── hooks/
│   ├── post-update               # Our custom hook (executable)
│   └── ...                       # Other default hooks
├── refs/
│   ├── heads/
│   │   └── master               # Master branch reference
│   └── tags/                    # Auto-generated tags
│       ├── release-2025-09-24
│       └── release-2025-09-25
└── objects/                     # Git objects storage

/usr/src/kodekloudrepos/media/    # Working repository
├── .git/                        # Git metadata
├── feature                      # Feature branch (local)
├── master                       # Master branch (local)
└── ...                         # Project files
```

---

## ✅ Final Outcome

### What We Achieved
- ✅ **Automated Tagging:** Every push to `master` creates a release tag
- ✅ **Consistent Format:** Tags follow `release-YYYY-MM-DD` pattern  
- ✅ **Zero Manual Effort:** No developer intervention required
- ✅ **Deployment Tracking:** Easy to track releases by date
- ✅ **Team Workflow:** Seamless integration with existing Git workflow

### Benefits
- **Consistency:** Standardized release naming convention
- **Automation:** Reduces human error and forgotten tags
- **Traceability:** Easy to identify release dates
- **Integration:** Works with existing CI/CD pipelines
- **Simplicity:** Minimal setup, maximum impact

---

## 🔧 Troubleshooting

### Common Issues

| Issue | Symptom | Solution |
|-------|---------|----------|
| **Hook not executing** | No tags created after push | Check if hook is executable: `chmod +x post-update` |
| **Permission denied** | Hook fails with permissions error | Ensure proper file permissions and ownership |
| **Tag creation fails** | Error in hook execution | Verify Git directory context in hook script |
| **Wrong tag name** | Incorrect date format | Check `date +%F` command output |

### Debug Commands
```bash
# Test hook manually
cd /opt/official.git/hooks
./post-update

# Check hook permissions
ls -la post-update

# View hook execution log
tail -f /var/log/git/post-update.log  # if logging enabled
```

---

## 📝 Maintenance Notes

### Regular Maintenance
- Monitor disk space as tags accumulate over time
- Consider archiving old tags periodically
- Review hook logs for any errors
- Test hook after Git server updates

### Future Enhancements
- Add email notifications when tags are created
- Include commit count or version incrementing
- Integration with deployment systems
- Conditional tagging based on branch patterns

---

*This automated tagging system ensures consistent release tracking and helps the team maintain deployment history with minimal manual overhead.*