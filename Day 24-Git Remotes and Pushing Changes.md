# 🔗 Git Remotes and Pushing Changes (Cluster Repo Lab)

In this lab we worked on the **`/usr/src/kodekloudrepos/cluster`** repository and learned how to manage **Git remotes**, commit changes, and push them to the right destination.

---

## ❓ What is a Remote in Git?

A **remote** in Git is simply a reference (a shortcut name) to another repository, usually on another server.  
- Example: `origin` is the default remote name when you clone a repo.  
- We can add more remotes to collaborate with different repos.  
- Task of a remote: It allows us to **push** our commits and **fetch/pull** changes from that remote repository.

---

## 📝 Task Requirements

1. Add a new remote named `dev_cluster` pointing to `/opt/xfusioncorp_cluster.git`.
2. Copy `/tmp/index.html` into the repo.
3. Commit the file to the **master** branch.
4. Push the master branch to the new remote.

---

## ⚙️ Steps We Did

### 1. Navigate to repo
```bash
cd /usr/src/kodekloudrepos/cluster
```

### 2. Add new remote
```bash
git remote add dev_cluster /opt/xfusioncorp_cluster.git
```
✅ Now `dev_cluster` points to the new repository.

### 3. Copy index.html into repo
```bash
cp /tmp/index.html .
```

### 4. Stage and commit
```bash
git add index.html
git commit -m "Add index.html file"
```

### 5. Push master branch to new remote
```bash
git push dev_cluster master
```

---

## ⚠️ Issue Faced: Dubious Ownership

When trying Git commands, we saw:
```
fatal: detected dubious ownership in repository at '/usr/src/kodekloudrepos/cluster'
```

👉 **Reason:** The repo directory was owned by root but Git was run as a normal user.  
Git 2.35+ checks repo ownership for security.

### ✅ Fix
We made Git trust this directory by marking it safe:
```bash
git config --global --add safe.directory /usr/src/kodekloudrepos/cluster
```
*(Alternative: change ownership with `sudo chown -R $USER:$USER /usr/src/kodekloudrepos/cluster`.)*

---

## 🎯 Final Verification

### Check remotes:
```bash
git remote -v
```
Output should list `dev_cluster` with `/opt/xfusioncorp_cluster.git`.

### Check commit log:
```bash
git log --oneline
```