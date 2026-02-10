# 02324 Advanced Programming Notes

These are my personal notes for the course **02324 Advanced Programming** at DTU. You are welcome to use these notes for studying, reference, or as a starting point for your own learning. Contributions and suggestions are also welcome!

These notes are structured for use with [Obsidian](https://obsidian.md/), a free note taking app. You can use them as a regular folder of Markdown files, but using Obsidian is recommended for better navigation and linking between notes.

## Getting Started

You have two main options for using these notes:

1. **Simple Download**: Download and use the notes as-is (no updates)
2. **Fork & Sync**: Create your own fork to add personal notes while receiving updates

**I recommend Option 2** if you plan to add your own notes and want to keep receiving updates to the course material.

## Setup Instructions

### Prerequisites

1. Install [Obsidian](https://obsidian.md/) (free)
2. Install [Git](https://git-scm.com/) (if you want to sync updates)

### macOS

#### Option 1: Simple Download (No Updates)

1. Click the green **Code** button on GitHub → **Download ZIP**
2. Extract the ZIP file to your desired location
3. Open Obsidian
4. Click **Open folder as vault**
5. Select the extracted folder

#### Option 2: Fork & Sync (Recommended)

1. **Fork this repository** (click Fork button on GitHub)

2. **Clone your fork locally:**
   ```bash
   # Replace YOUR_USERNAME with your GitHub username
   git clone https://github.com/YOUR_USERNAME/02324-Advanced-Programming-Notes.git
   cd 02324-Advanced-Programming-Notes
   ```

3. **Add the original repository as upstream:**
   ```bash
   git remote add upstream https://github.com/lassedtu/02324-Advanced-Programming-Notes.git
   ```

4. **Open in Obsidian:**
   - Open Obsidian
   - Click **Open folder as vault**
   - Select the cloned folder

### Windows

#### Option 1: Simple Download (No Updates)

1. Click the green **Code** button on GitHub → **Download ZIP**
2. Extract the ZIP file using Windows Explorer or 7-Zip
3. Open Obsidian
4. Click **Open folder as vault**
5. Select the extracted folder

#### Option 2: Fork & Sync (Recommended)

1. **Fork this repository** (click Fork button on GitHub)

2. **Clone your fork locally:**
   ```cmd
   # Open Command Prompt or PowerShell
   # Replace YOUR_USERNAME with your GitHub username
   git clone https://github.com/YOUR_USERNAME/02324-Advanced-Programming-Notes.git
   cd 02324-Advanced-Programming-Notes
   ```

3. **Add the original repository as upstream:**
   ```cmd
   git remote add upstream https://github.com/lassedtu/02324-Advanced-Programming-Notes.git
   ```

4. **Open in Obsidian:**
   - Open Obsidian
   - Click **Open folder as vault**
   - Select the cloned folder

### Linux

#### Option 1: Simple Download (No Updates)

1. Click the green **Code** button on GitHub → **Download ZIP**
2. Extract the ZIP file:
   ```bash
   unzip 02324-Advanced-Programming-Notes-main.zip
   ```
3. Open Obsidian
4. Click **Open folder as vault**
5. Select the extracted folder

#### Option 2: Fork & Sync (Recommended)

1. **Fork this repository** (click Fork button on GitHub)

2. **Clone your fork locally:**
   ```bash
   # Replace YOUR_USERNAME with your GitHub username
   git clone https://github.com/YOUR_USERNAME/02324-Advanced-Programming-Notes.git
   cd 02324-Advanced-Programming-Notes
   ```

3. **Add the original repository as upstream:**
   ```bash
   git remote add upstream https://github.com/lassedtu/02324-Advanced-Programming-Notes.git
   ```

4. **Open in Obsidian:**
   - Open Obsidian (install via package manager or download from website)
   - Click **Open folder as vault**
   - Select the cloned folder

## Keeping Your Vault Updated

If you used the **Fork & Sync** method, you can regularly pull updates from the original repository while keeping your personal notes.

### Step 1: Commit Your Personal Changes

Before updating, always commit your personal notes:

```bash
# Add your changes
git add .
git commit -m "Add my personal notes"
git push origin main
```

### Step 2: Fetch and Merge Updates

```bash
# Fetch latest changes from the original repository
git fetch upstream

# Merge the updates into your fork
git merge upstream/main

# Push the updates to your fork
git push origin main
```

### Handling Merge Conflicts

If you've modified the same files that were updated in the original repository, you may encounter merge conflicts:

1. Git will mark conflicted files
2. Open the files in Obsidian or a text editor
3. Look for conflict markers (`<<<<<<`, `======`, `>>>>>>`)
4. Choose which version to keep or combine both
5. Remove the conflict markers
6. Commit the resolved conflicts:
   ```bash
   git add .
   git commit -m "Resolve merge conflicts"
   git push origin main
   ```

---

Feel free to use, modify, or share these notes as you wish. 

If you have any questions or suggestions, please open an issue or submit a pull request on GitHub.
