# 🤖 Automation Guide for PyPI Publishing

## ❓ Important Understanding

### GitHub vs PyPI - They Are Independent!

```
┌──────────────┐                    ┌──────────────┐
│   GitHub     │  NOT CONNECTED!    │    PyPI      │
│  (Git Repo)  │ ←──────────────→   │  (Packages)  │
└──────────────┘                    └──────────────┘
```

**Key Points**:
- ❌ PyPI does **NOT** automatically fetch from GitHub
- ✅ You **MUST** manually publish each version to PyPI
- ✅ You **CAN** automate this with GitHub Actions

---

## 🔄 The Manual Workflow (What You Did)

```bash
# 1. Make changes in code
# 2. Update version in setup.py and pyproject.toml
# 3. Build the package
python -m build

# 4. Upload to PyPI
twine upload dist/*

# 5. Commit and push to GitHub
git add .
git commit -m "Release v1.0.1"
git push origin dev
```

**Problem**: You have to do this **every single time** you want to release!

---

## ✨ The Automated Workflow (Better!)

I've created **2 GitHub Actions** for you:

### 1. `publish-to-pypi.yml` (Production)
**Triggers when**: You create a GitHub Release

### 2. `publish-to-test-pypi.yml` (Testing)
**Triggers when**: You push to `dev` branch or create test tags

---

## 🚀 How to Set Up Automation

### Step 1: Create PyPI API Tokens

#### For Production PyPI:
1. Go to: https://pypi.org/manage/account/token/
2. Click "Add API token"
3. Name: `GitHub Actions - dsa-toolkit`
4. Scope: Select "Project: dsa-toolkit"
5. Click "Add token"
6. **COPY THE TOKEN** (starts with `pypi-...`)

#### For Test PyPI:
1. Go to: https://test.pypi.org/manage/account/token/
2. Same steps as above
3. Name: `GitHub Actions - dsa-toolkit (Test)`

---

### Step 2: Add Tokens to GitHub Secrets

1. Go to: https://github.com/masoomverma/dsa-toolkit/settings/secrets/actions

2. Click "New repository secret"

3. Add **TWO** secrets:

   **Secret 1:**
   - Name: `PYPI_API_TOKEN`
   - Value: (paste your PyPI token)
   
   **Secret 2:**
   - Name: `TEST_PYPI_API_TOKEN`
   - Value: (paste your Test PyPI token)

4. Click "Add secret" for each

---

### Step 3: Push the GitHub Actions Files

```powershell
# Add the new workflow files
git add .github/workflows/

# Commit
git commit -m "Add GitHub Actions for automated PyPI publishing"

# Push to GitHub
git push origin dev
```

---

## 🎯 How to Use the Automation

### Method 1: Automated Release to PyPI (Recommended)

```bash
# 1. Make your changes
# Edit your files...

# 2. Update version number
# In setup.py: version="1.0.1"
# In pyproject.toml: version = "1.0.1"
# In dsa/__init__.py: __version__ = "1.0.1"

# 3. Commit and push to dev
git add .
git commit -m "Add new feature X"
git push origin dev

# 4. Merge to main (when ready for release)
git checkout main
git merge dev
git push origin main

# 5. Create a GitHub Release
# Go to: https://github.com/masoomverma/dsa-toolkit/releases/new
# - Tag: v1.0.1
# - Title: Release v1.0.1
# - Description: What's new
# - Click "Publish release"

# 🎉 GitHub Actions will automatically:
#    - Build the package
#    - Run checks
#    - Upload to PyPI
```

### Method 2: Manual Trigger (if workflow_dispatch enabled)

1. Go to: https://github.com/masoomverma/dsa-toolkit/actions
2. Select "Publish to PyPI"
3. Click "Run workflow"
4. Select branch
5. Click "Run workflow"

---

## 📋 Complete Release Checklist

When you want to release a new version:

- [ ] **1. Update Version Numbers** (3 places)
  - [ ] `setup.py` → `version="1.0.1"`
  - [ ] `pyproject.toml` → `version = "1.0.1"`
  - [ ] `dsa/__init__.py` → `__version__ = "1.0.1"`

- [ ] **2. Update CHANGELOG.md**
  ```markdown
  ## [1.0.1] - 2025-10-XX
  ### Added
  - New feature description
  
  ### Fixed
  - Bug fix description
  ```

- [ ] **3. Test Locally**
  ```powershell
  python -m build
  twine check dist/*
  ```

- [ ] **4. Commit and Push to dev**
  ```bash
  git add .
  git commit -m "Release v1.0.1"
  git push origin dev
  ```

- [ ] **5. Merge to main (Optional but recommended)**
  ```bash
  git checkout main
  git merge dev
  git push origin main
  ```

- [ ] **6. Create GitHub Release**
  - Go to: https://github.com/masoomverma/dsa-toolkit/releases/new
  - Tag: `v1.0.1`
  - Title: `Release v1.0.1`
  - Description: List changes
  - Click "Publish release"

- [ ] **7. Verify Automation**
  - Check: https://github.com/masoomverma/dsa-toolkit/actions
  - Wait for green checkmark ✅
  - Verify on PyPI: https://pypi.org/project/dsa-toolkit/

---

## 🔍 Monitoring Your Releases

### Check GitHub Actions Status:
https://github.com/masoomverma/dsa-toolkit/actions

### Check PyPI Package:
https://pypi.org/project/dsa-toolkit/

### Check Download Stats (after 24-48 hours):
https://pypistats.org/packages/dsa-toolkit

---

## 🐛 Troubleshooting

### Action fails with "401 Unauthorized"
- **Cause**: Invalid or missing PyPI API token
- **Fix**: Check GitHub Secrets → Re-add token

### Action fails with "File already exists"
- **Cause**: Version already published to PyPI
- **Fix**: Bump version number in all 3 files

### Action doesn't trigger
- **Cause**: Workflow file not in `main` branch
- **Fix**: Make sure `.github/workflows/` exists in main branch

---

## 💡 Best Practices

1. **Use Semantic Versioning**:
   - `1.0.0` → Major release
   - `1.0.1` → Bug fix
   - `1.1.0` → New feature
   - `2.0.0` → Breaking changes

2. **Test on Test PyPI First**:
   - Use `v1.0.1-test` tag
   - Verify installation works
   - Then create official release

3. **Keep CHANGELOG Updated**:
   - Always document what changed
   - Users appreciate knowing what's new

4. **Use Branches**:
   - `dev` → Development work
   - `main` → Stable releases only

---

## 🎯 Quick Reference

### To Release a New Version:

```bash
# 1. Update versions (3 files)
# 2. Update CHANGELOG.md
# 3. Commit and push
git add .
git commit -m "Release v1.0.1"
git push origin dev

# 4. Create GitHub Release (on GitHub website)
# 5. Wait for automation ✅
```

**That's it! Automation does the rest!** 🎉

---

## 📚 Additional Resources

- **GitHub Actions Docs**: https://docs.github.com/en/actions
- **PyPI API Tokens**: https://pypi.org/help/#apitoken
- **Twine Docs**: https://twine.readthedocs.io/
- **Semantic Versioning**: https://semver.org/

---

**Created**: October 3, 2025  
**For**: dsa-toolkit v1.0.0  
**By**: Masoom Verma
