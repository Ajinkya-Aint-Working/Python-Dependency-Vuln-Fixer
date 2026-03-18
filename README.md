# Python-Dependency-Vuln-Fixer

This guide covers:
- Finding root-level packages
- Managing dependencies safely
- Fixing vulnerabilities without breaking code
- Using pip-compile + pip-audit correctly

---

## 🚀 1. Identify Root-Level Packages

### ✅ Why this matters
Your project may have 300+ packages ❌  
But only ~20 are actually important ✅

---

### 🔍 Get top-level dependencies

```bash
pip list --not-required
```

---

### 🔍 View dependency tree

```bash
pipdeptree
```

---

### 🔍 Find parent of a package

```bash
pipdeptree -r -p <package>
```

Example:

```bash
pipdeptree -r -p ecdsa
```

---

## 📦 2. Proper Dependency Structure

### ❌ WRONG

```
requirements.txt (300+ packages)
```

---

### ✅ CORRECT

#### requirements.in (top-level only)

```
fastapi
Django>=5.2.12
langchain
python-jose
```

---

#### requirements.txt (auto-generated)

```
# DO NOT EDIT manually
```

---

## ⚙️ 3. Install Tools

```bash
pip install pip-tools
pip install pip-audit
```

---

## 🔄 4. Compile Dependencies

### Generate lock file

```bash
pip-compile
```

---

### Upgrade all packages

```bash
pip-compile --upgrade
```

---

### Upgrade specific package (SAFE)

```bash
pip-compile --upgrade-package Django
```

---

## 📥 5. Install Dependencies

### Best way (clean install)

```bash
pip-sync
```

---

### OR normal install

```bash
pip install -r requirements.txt
```

---

## 🔍 6. Check Vulnerabilities

```bash
pip-audit
```

OR

```bash
pip-audit -r requirements.txt
```

---

## 🧠 7. Fix Vulnerabilities (CORRECT WAY)

### ❌ NEVER DO

```bash
pip-audit --fix
```

---

### ✅ DO THIS INSTEAD

#### Step 1: Identify vulnerable package

Example:

```
ecdsa 0.19.1 → vulnerable
```

---

#### Step 2: Find parent

```bash
pipdeptree -r -p ecdsa
```

---

### Case A: Top-level package

Update `requirements.in`:

```
Django>=5.2.12
```

Then:

```bash
pip-compile --upgrade
pip-sync
```

---

### Case B: Transitive dependency

Add constraint:

```
ecdsa>=0.19.2
```

Then:

```bash
pip-compile --upgrade-package ecdsa
pip-sync
```

---

### Case C: Dependency conflict

Upgrade parent package:

```bash
pip-compile --upgrade-package python-jose
```

---

## 🔍 8. Verify Everything

```bash
pip check
```

Expected:

```
No broken requirements found.
```

---

```bash
pip-audit
```

Expected:

```
No known vulnerabilities found
```

---

## 🧪 9. Fresh Installation Test

```bash
rm -rf .venv

python -m venv .venv
source .venv/Scripts/activate

pip install pip-tools

pip-compile
pip-sync

pip check
pip-audit
```

---

## 🐳 10. Docker Validation

```bash
docker build -t my-app .
```

```bash
docker run --rm -v /var/run/docker.sock:/var/run/docker.sock \
  aquasec/trivy image my-app
```

---

## 🧠 Key Rules

### ✅ DO
- Use `requirements.in`
- Use `pip-compile`
- Upgrade selectively
- Fix transitive deps via constraints

---

### ❌ DON’T
- Edit `requirements.txt` manually
- Use `pip-audit --fix`
- Pin hundreds of dependencies manually
- Ignore dependency conflicts

---

## 🔥 Real Workflow

```bash
# detect
pip-audit

# fix safely
pip-compile --upgrade-package <package>

# install
pip-sync

# verify
pip check
pip-audit
```

---

## 🎯 Final Outcome

- ✅ Clean dependency tree  
- ✅ No vulnerabilities  
- ✅ Reproducible builds  
- ✅ Production-ready setup  
