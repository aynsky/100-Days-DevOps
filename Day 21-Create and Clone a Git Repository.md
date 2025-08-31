# Day 21: Create and Clone a Git Repository

## 🎯 Task

Set up a Git repository for the Nautilus development team on the Storage Server.

### Requirements

- Install Git using yum
- Create a bare repository named `/opt/games.git`
- Clone the repository to `/usr/src/kodekloudrepos`, ensuring the correct structure

---

## ✅ Steps

### 1. Install Git

Install the Git package on the server:

```bash
sudo yum install git -y
```

### 2. Create Bare Repository

Initialize a bare Git repository at the specified path:

```bash
sudo git init --bare /opt/games.git
```

### 3. Clone Repository with Correct Structure

Choose one of the following options based on the desired structure:

#### Option A: Clone Directly into Target Folder

Clone the repository directly into `/usr/src/kodekloudrepos`:

```bash
git clone /opt/games.git /usr/src/kodekloudrepos
```

This places the repository's `.git` folder and all files at the top level of `/usr/src/kodekloudrepos`.

#### Option B: Clone into a Subdirectory (Recommended)

Clone the repository into a subdirectory for a cleaner structure:

```bash
cd /usr/src/kodekloudrepos
git clone /opt/games.git games
```

This places the repository under `/usr/src/kodekloudrepos/games`.

- Keeps the parent directory organized and allows multiple repositories in `/usr/src/kodekloudrepos`

---

## 🔍 Verification

Verify the repository exists and has the correct structure:

```bash
ls -l /usr/src/kodekloudrepos
```

If using Option B (subdirectory method), also check:

```bash
ls -l /usr/src/kodekloudrepos/games
```

### Expected Output

- **For Option A:** The `.git` folder and any future files will appear directly in `/usr/src/kodekloudrepos`
- **For Option B:** A `games` subdirectory will exist in `/usr/src/kodekloudrepos`, containing the `.git` folder and any future files

---

## 📖 Explanation

### Bare Repository

- A bare repository contains only Git metadata and no working files (no checked-out files or working tree)
- It serves as a central repository for team collaboration
- Developers clone this repository and push/pull changes to it
- By convention, bare repositories often end with `.git` (e.g., `games.git`)

### Cloning Options

**Directly into a folder:**
- Places repository files at the top level of the specified directory (`/usr/src/kodekloudrepos`)
- Suitable for single-repository setups

**Into a subdirectory:**
- Places the repository in a named folder (e.g., `/usr/src/kodekloudrepos/games`)
- Recommended for organizing multiple repositories under the same parent directory

---

## 🎯 Highlights

- **Git Installation:** Ensures Git is available for repository management
- **Bare Repository Creation:** The `--bare` flag creates a repository without a working directory, ideal for shared use
- **Cloning Flexibility:** Choose between direct cloning or subdirectory cloning based on organizational needs
- **Verification:** Checking the directory structure confirms the repository is set up and cloned correctly

---

## 📋 Complete Command Summary

```bash
# Install Git
sudo yum install git -y

# Create bare repository
sudo git init --bare /opt/games.git

# Option A: Direct clone
git clone /opt/games.git /usr/src/kodekloudrepos

# Option B: Subdirectory clone (Recommended)
cd /usr/src/kodekloudrepos
git clone /opt/games.git games

# Verification
ls -l /usr/src/kodekloudrepos
ls -l /usr/src/kodekloudrepos/games  # If using Option B
```