# Task: Create New Git Branch on Storage Server in Stratos DC

## Task Description

Create a new branch `xfusioncorp_official` from the `master` branch in the Git repository located at `/usr/src/kodekloudrepos/official`.

## Problem Encountered

When attempting to switch to the `master` branch, the following error occurred:

```
git checkout master
fatal: detected dubious ownership in repository at '/usr/src/kodekloudrepos/official'
To add an exception for this directory, call:

        git config --global --add safe.directory /usr/src/kodekloudrepos/official
```

This happens because Git detected that the directory is owned by a different user, which it considers potentially unsafe.

## Solution

1. **Mark the repository as safe:**

```bash
git config --global --add safe.directory /usr/src/kodekloudrepos/official
```

2. **Navigate to the repository:**

```bash
cd /usr/src/kodekloudrepos/official
```

3. **Ensure you are on the latest `master` branch:**

```bash
git checkout master
git pull
```

4. **Create and switch to the new branch:**

```bash
git checkout -b xfusioncorp_official
```

5. **Verify the new branch:**

```bash
git branch
```

Expected output:

```
* xfusioncorp_official
  master
```

6. **Push the branch to the remote repository (optional):**

```bash
git push origin xfusioncorp_official
```

✅ **Notes:**

* Using `--global` makes this safe for all repositories for the user.
* Alternatively, you can mark only this repo as safe without affecting other repos:

```bash
git config --add safe.directory /usr/src/kodekloudrepos/official
```
