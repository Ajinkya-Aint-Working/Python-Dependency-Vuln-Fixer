#  Node.js Dependency-Vuln-Fixer

This guide covers:
- Finding root-level packages
- Managing dependencies safely
- Fixing vulnerabilities without breaking code
- Using npm audit, overrides, and lockfiles correctly

---

## 🚀 1. Identify Root-Level Packages

### ✅ Why this matters
Your project may have 1000+ packages ❌  
But only direct dependencies matter ✅

---

### 🔍 Get top-level dependencies

```bash
npm ls --depth=0
```

OR

```bash
cat package.json
```

---

### 🔍 View full dependency tree

```bash
npm ls
```

---

### 🔍 Find parent of a package

```bash
npm ls <package>
```

Example:

```bash
npm ls ecdsa
```

---

## 📦 2. Proper Dependency Structure

### ❌ WRONG

node_modules/ (1000+ uncontrolled packages)  
package.lock.json (messy)

---

### ✅ CORRECT

#### package.json (top-level only)

```json
{
  "dependencies": {
    "express": "^4.19.2",
    "jsonwebtoken": "^9.0.2",
    "mongoose": "^8.5.0"
  }
}
```

---

#### package-lock.json

AUTO-GENERATED — DO NOT EDIT

---

## ⚙️ 3. Install Tools

```bash
npm audit
```

Optional:

```bash
npm install -g npm-check-updates
npm install -g pnpm
```

---

## 🔄 4. Install Dependencies (Clean Way)

### Fresh install

```bash
rm -rf node_modules package-lock.json
npm install
```

---

### CI / Production install

```bash
npm ci
```

---

## 🔍 5. Check Vulnerabilities

```bash
npm audit
```

---

## 🧠 6. Fix Vulnerabilities (CORRECT WAY)

### ❌ NEVER DO --force

```bash
npm audit fix --force
```

---

### ✅ SAFE APPROACH

#### Step 1: Identify vulnerable package

Example:

ecdsa → vulnerable

---

#### Step 2: Find parent

```bash
npm ls ecdsa
```

---

### Case A: Top-level dependency

```bash
npm install jsonwebtoken@latest
```

---

### Case B: Transitive dependency

#### package.json

```json
{
  "overrides": {
    "ecdsa": "^0.19.2"
  }
}
```

```bash
npm install
```

---

### Case C: Dependency conflict

```bash
npm install jsonwebtoken@latest
```

---

## 🔍 7. Verify Everything

```bash
npm ls
npm audit
```

Expected:

found 0 vulnerabilities

---

## 🧪 8. Fresh Installation Test

```bash
rm -rf node_modules package-lock.json
npm install
npm audit
```

---

## 🐳 9. Docker Validation

```bash
docker build -t my-node-app .
```

```bash
docker run --rm -v /var/run/docker.sock:/var/run/docker.sock \
  aquasec/trivy image my-node-app
```

---

## 🧠 10. Advanced

### 🔒 Lock dependency versions
Commit package-lock.json

---

### ⚡ Use npm ci in production

```bash
npm ci
```

---

### 🔄 Check outdated packages

```bash
npm outdated
```

---

### 🚀 Bulk upgrade safely

```bash
npx npm-check-updates -u
npm install
```

---

## 🔥 Real Workflow

```bash
npm audit
npm ls <package>
npm install <package>@latest
npm install
npm audit
```

---

## 🧠 Key Rules

### ✅ DO
- Use package.json cleanly
- Commit package-lock.json
- Use overrides for transitive deps
- Upgrade selectively

---

### ❌ DON’T
- Use npm audit fix --force
- Delete lockfile in production
- Ignore dependency tree
- Install random packages blindly

---

## 🎯 Final Outcome

- Clean dependency tree  
- No vulnerabilities  
- Reproducible builds  
- Production-ready Node.js setup  
