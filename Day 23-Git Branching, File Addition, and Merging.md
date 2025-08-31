# E-commerce Repository: Adding `index.html` in `datacenter` Branch

This guide outlines the steps to create a new **datacenter** branch, add `index.html` from `/tmp/index.html`, merge it into `master`, and push changes to the remote repository.

---

## Steps

### 1. Navigate to the repository

Change to the ecommerce repository directory:

```bash
cd /usr/src/kodekloudrepos/ecommerce
```

### 2. Ensure master is up-to-date

Switch to the master branch and pull the latest changes:

```bash
git checkout master
git pull
```

### 3. Create and switch to a new branch `datacenter`

```bash
git checkout -b datacenter
```

### 4. Copy `index.html` into the repository

Copy the `index.html` file to the root of the repository (or a subdirectory if needed):

```bash
cp /tmp/index.html .
```

### 5. Stage and commit the file

```bash
git add index.html
git commit -m "Add index.html in datacenter branch"
```

### 6. Switch back to master

```bash
git checkout master
```

### 7. Merge `datacenter` branch into `master`

```bash
git merge datacenter
```

> **Note:** Resolve any conflicts if they occur (unlikely in this case).

### 8. Push both branches to origin

```bash
git push origin master
git push origin datacenter
```

---

## Summary of Commands

```bash
git checkout -b datacenter          # Create new branch from master
cp /tmp/index.html .                # Copy file into repo
git add index.html                  # Stage file
git commit -m "Add index.html in datacenter branch"  # Commit file
git checkout master                 # Switch back to master
git merge datacenter                # Merge datacenter into master
git push origin master              # Push master branch
git push origin datacenter          # Push datacenter branch
```

## Additional Notes

- Ensure you have the necessary permissions to push to the remote repository
- The `index.html` file should exist at `/tmp/index.html` before running these commands
- This workflow creates a feature branch, adds the file, and then merges it back to master
- Both branches will be available on the remote repository after completion