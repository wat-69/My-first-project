# คู่มือ GitHub Advanced ระดับมืออาชีพ
*พร้อมการใช้งานกับ Vibe (Mistral AI)*

---

## 📚 สารบัญ
1. [ภาค 1: Git Advanced](#ภาค-1-git-advanced)
   - [1.1 Git Internals](#11-git-internals)
   - [1.2 Rebase vs Merge](#12-rebase-vs-merge)
   - [1.3 Git Hooks](#13-git-hooks)
   - [1.4 Git LFS](#14-git-lfs)
   - [1.5 Git Aliases & Power Tips](#15-git-aliases--power-tips)
2. [ภาค 2: GitHub Advanced Features](#ภาค-2-github-advanced-features)
   - [2.1 GitHub Actions (CI/CD)](#21-github-actions-cicd)
   - [2.2 GitHub CLI (`gh`)](#22-github-cli-gh)
   - [2.3 GitHub API (REST & GraphQL)](#23-github-api-rest--graphql)
   - [2.4 GitHub Webhooks](#24-github-webhooks)
   - [2.5 GitHub Codespaces & Copilot](#25-github-codespaces--copilot)
3. [ภาค 3: Collaboration & Workflow](#ภาค-3-collaboration--workflow)
   - [3.1 Pull Request Templates](#31-pull-request-templates)
   - [3.2 Branch Protection Rules](#32-branch-protection-rules)
   - [3.3 CODEOWNERS](#33-codeowners)
   - [3.4 GitHub Projects](#34-github-projects)
4. [ภาค 4: Automation & Scripting](#ภาค-4-automation--scripting)
   - [4.1 Custom GitHub Actions](#41-custom-github-actions)
   - [4.2 GitHub Apps vs OAuth Apps](#42-github-apps-vs-oauth-apps)
   - [4.3 GitHub Secret Scanning](#43-github-secret-scanning)
5. [ภาค 5: Troubleshooting & Best Practices](#ภาค-5-troubleshooting--best-practices)
   - [5.1 Merge Conflicts ขั้นสูง](#51-merge-conflicts-ขั้นสูง)
   - [5.2 Git Recovery](#52-git-recovery)
   - [5.3 GitHub Rate Limits](#53-github-rate-limits)
6. [ภาค 6: การใช้ GitHub กับ Vibe (Mistral AI)](#ภาค-6-การใช้-github-กับ-vibe-mistral-ai)
   - [6.1 Vibe คืออะไร](#61-vibe-คืออะไร)
   - [6.2 การเชื่อมต่อ Vibe กับ GitHub](#62-การเชื่อมต่อ-vibe-กับ-github)
   - [6.3 การทำงานกับ Vibe ใน GitHub](#63-การทำงานกับ-vibe-ใน-github)
   - [6.4 Best Practices & Security](#64-best-practices--security)
7. [ภาค 7: การอัพโหลดไฟล์เข้า GitHub เพื่อให้ Vibe นำไปใช้งาน](#ภาค-7-การอัพโหลดไฟล์เข้า-github-เพื่อให้-vibe-นำไปใช้งาน)
   - [7.1 วิธีการอัพโหลดไฟล์](#71-วิธีการอัพโหลดไฟล์)
   - [7.2 โครงสร้างไฟล์ที่เหมาะสม](#72-โครงสร้างไฟล์ที่เหมาะสม)
   - [7.3 Conventional Commits](#73-conventional-commits)
   - [7.4 `.gitignore`](#74-gitignore)
   - [7.5 ตัวอย่าง Workflow](#75-ตัวอย่าง-workflow)
8. [ภาค 8: ภาคผนวก](#ภาค-8-ภาคผนวก)
   - [8.1 Cheat Sheets](#81-cheat-sheets)
   - [8.2 Glossary](#82-glossary)
   - [8.3 แหล่งอ้างอิง](#83-แหล่งอ้างอิง)
## ภาค 1: Git Advanced
### 1.1 Git Internals
Git ทำงานอย่างไรภายใต้?
#### 🔹 Git Objects
Git จัดเก็บข้อมูลในรูปแบบ **Objects** 4 ประเภท:
```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│   Blob      │────▶│    Tree     │────▶│   Commit    │
│ (File Data) │     │ (Directory) │     │ (Metadata)  │
└─────────────┘     └─────────────┘     └─────────────┘
                                                      │
                                                      ▼
                                               ┌─────────────┐
                                               │    Tag      │
                                               │ (Version)   │
                                               └─────────────┘
```
- **Blob**: เก็บข้อมูลของไฟล์ (SHA-1 Hash)
  - *ตัวอย่าง*: `git hash-object -w hello.txt`
- **Tree**: เก็บโครงสร้างไดเรกทอรี (ชี้ไปยัง Blobs และ Trees ย่อย)
- **Commit**: เก็บ Metadata (Author, Date, Message) และชี้ไปยัง Tree
- **Tag**: ชี้ไปยัง Commit (ใช้สำหรับ Versioning)
#### 🔹 Git Index (Staging Area)
- เป็นพื้นที่ระหว่าง **Working Directory** และ **Repository**
- *คำสั่ง*: `git add` = เตรียมไฟล์ไปยัง Index
#### 🔹 Git Refs (References)
- **HEAD**: ชี้ไปยัง Branch ปัจจุบัน
- **Branches**: ชี้ไปยัง Commit ล่าสุด
- **Tags**: ชี้ไปยัง Commit เฉพาะ
> 💡 **Important**: 
> **<span style="color:#F39C12">Git ใช้ SHA-1 Hash ในการอ้างอิง Objects ทุกชนิด</span>**
> *ตัวอย่าง*: `git cat-file -p 6ff87c` (ดูข้อมูลของ Object)
#### 📌 แหล่งอ้างอิง
- [Git Internals - Official Git Book](https://git-scm.com/book/en/v2/Git-Internals-Plumbing-and-Porcelain)
- [Git Objects Explained](https://git-scm.com/book/en/v2/Git-Internals-Git-Objects)
### 1.2 Rebase vs Merge
#### 🔹 Merge
- **คือ**: สร้าง **Merge Commit** เพื่อรวม Branch
- **ข้อดี**: ประวัติ (History) ชัดเจน
- **ข้อเสีย**: มี Merge Commits เยอะ
*ตัวอย่าง*:
```bash
git checkout main
git merge feature-branch
```
#### 🔹 Rebase
- **คือ**: ย้าย Base ของ Branch ไปยัง Commit ล่าสุดของ Target Branch
- **ข้อดี**: ประวัติ (History) สะอาด
- **ข้อเสีย**: อาจทำให้ Conflict ยากขึ้น
*ตัวอย่าง*:
```bash
git checkout feature-branch
git rebase main
```
#### 🔹 Interactive Rebase
- **คือ**: แก้ไข, รวม, หรือลบ Commits
- *คำสั่ง*: `git rebase -i HEAD~3`
*ตัวอย่าง*:
```
# ใน Interactive Rebase Menu:
pick a1b2c3 Feat: Add login
squash d4e5f6 Fix: Typo in login
pick g7h8i9 Feat: Add logout
```
> ⚠️ **Warning**: 
> **<span style="color:#F39C12">อย่า Rebase Commits ที่ได้ Push ไปยัง Remote แล้ว!</span>**
#### 📌 แหล่งอ้างอิง
- [Git Branching - Rebase](https://git-scm.com/book/en/v2/Git-Branching-Rebasing)
- [Merge vs Rebase](https://www.atlassian.com/git/tutorials/merging-vs-rebasing)
### 1.3 Git Hooks
Git Hooks คือ **Scripts** ที่รันอัตโนมัติเมื่อเกิด Event ต่างๆ
#### 🔹 Hooks ประเภทต่างๆ
| Hook | เมื่อรัน | ใช้งาน |
|------|--------|--------|
| `pre-commit` | ก่อน `git commit` | Run Tests, Linting |
| `post-commit` | หลัง `git commit` | Log, Notify |
| `pre-push` | ก่อน `git push` | Run Full Tests |
| `post-merge` | หลัง `git merge` | Update Dependencies |
#### 🔹 ตัวอย่าง: `pre-commit` Hook
1. สร้างไฟล์: `.git/hooks/pre-commit`
2. ใส่ Script:
```bash
#!/bin/sh
# Run ESLint
eslint . || {
  echo "ESLint failed!"
  exit 1
}
```
3. ทำให้รันได้: `chmod +x .git/hooks/pre-commit`
> 💡 **Tip**: 
> **<span style="color:#F39C12">ใช้ `husky` เพื่อจัดการ Hooks ง่ายขึ้น</span>**
> *ตัวอย่าง*: `npx husky add .husky/pre-commit "npm test"`
#### 📌 แหล่งอ้างอิง
- [Git Hooks Documentation](https://git-scm.com/docs/githooks)
- [Husky - Git Hooks Made Easy](https://typicode.github.io/husky/)
### 1.4 Git LFS (Large File Storage)
Git LFS ช่วยจัดการไฟล์ขนาดใหญ่ (เช่น Video, Dataset, Binary)
#### 🔹 วิธีการใช้งาน
1. ติดตั้ง Git LFS:
   ```bash
   git lfs install
   ```
2. Track ไฟล์ขนาดใหญ่:
   ```bash
   git lfs track "*.psd"
   git lfs track "*.zip"
   ```
3. Add `.gitattributes`:
   ```bash
   git add .gitattributes
   ```
4. Commit และ Push:
   ```bash
   git add large_file.psd
git commit -m "Add large file via LFS"
git push origin main
   ```
> ⚠️ **Warning**: 
> **<span style="color:#F39C12">Git LFS มี Quota (1GB/Month ฟรี)</span>**
#### 📌 แหล่งอ้างอิง
- [Git LFS Official Docs](https://git-lfs.com/)
- [Git LFS Tutorial](https://git-lfs.github.com/)
### 1.5 Git Aliases & Power Tips
#### 🔹 Git Aliases
- สร้างคำสั่งย่อ:
  ```bash
  git config --global alias.co checkout
  git config --global alias.br branch
  git config --global alias.ci commit
  git config --global alias.st status
  ```
- *ตัวอย่าง*: `git co main` = `git checkout main`
#### 🔹 Git Stash ขั้นสูง
- Stash เฉพาะไฟล์:
  ```bash
  git stash push -m "temp changes" -- path/to/file
  ```
- Apply Stash เฉพาะ:
  ```bash
  git stash apply stash@{2}
  ```
#### 🔹 Git Bisect
- หา Commit ที่ทำให้ Bug เกิด:
  ```bash
  git bisect start
git bisect bad HEAD
git bisect good v1.0
git bisect run ./test-script.sh
  ```
#### 🔹 Git Partial Clone
- Clone เฉพาะ Branch:
  ```bash
  git clone --branch main --single-branch https://github.com/user/repo.git
  ```
- Clone เฉพาะ Depth:
  ```bash
  git clone --depth 1 https://github.com/user/repo.git
  ```
## ภาค 2: GitHub Advanced Features
### 2.1 GitHub Actions (CI/CD)
GitHub Actions ช่วย **อัตโนมัติ Workflow** เช่น:
- Run Tests
- Deploy Applications
- Build Docker Images
#### 🔹 โครงสร้าง Workflow
```yaml
# .github/workflows/deploy.yml
name: Deploy to Production
on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Install Dependencies
        run: npm install
      - name: Run Tests
        run: npm test
  deploy:
    needs: test
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Deploy
        run: ./deploy.sh
```
#### 🔹 Matrix Strategy
- Run Tests บนหลาย OS/Version:
```yaml
jobs:
  test:
    strategy:
      matrix:
        os: [ubuntu-latest, windows-latest, macos-latest]
        node: [14, 16, 18]
    runs-on: ${{ matrix.os }}
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v3
        with:
          node-version: ${{ matrix.node }}
      - run: npm test
```
#### 🔹 Caching
- เร่ง Speed ของ Workflow:
```yaml
steps:
  - uses: actions/cache@v3
    with:
      path: node_modules
      key: ${{ runner.os }}-node-${{ hashFiles('package-lock.json') }}
```
> 💡 **Tip**: 
> **<span style="color:#F39C12">ใช้ `actions/checkout@v4` เพื่อ Performance ที่ดีกว่า</span>**
#### 📌 แหล่งอ้างอิง
- [GitHub Actions Docs](https://docs.github.com/en/actions)
- [GitHub Actions Marketplace](https://github.com/marketplace?type=actions)
### 2.2 GitHub CLI (`gh`)
GitHub CLI (`gh`) ช่วยจัดการ GitHub ผ่าน Terminal
#### 🔹 ติดตั้ง
```bash
# Linux (Debian/Ubuntu)
sudo apt install gh
# macOS
brew install gh
# Windows
winget install --id GitHub.cli
```
#### 🔹 คำสั่งพื้นฐาน
| คำสั่ง | คำอธิบาย |
|--------|-----------|
| `gh auth login` | Login เข้าสู่ GitHub |
| `gh repo clone user/repo` | Clone Repository |
| `gh pr list` | ดู Pull Requests |
| `gh pr create` | สร้าง Pull Request |
| `gh issue list` | ดู Issues |
#### 🔹 ตัวอย่าง: สร้าง Pull Request
```bash
# Clone Repository
gh repo clone wat-69/My-first-project
cd My-first-project
# สร้าง Branch
git checkout -b feature/new-feature
# Commit Changes
git add .
git commit -m "feat: add new feature"
git push origin feature/new-feature
# สร้าง PR
gh pr create --title "Add New Feature" --body "Description here" --fill
```
> 💡 **Tip**: 
> **<span style="color:#F39C12">`--fill` จะดึง Title และ Body จาก Commit Messages</span>**
#### 📌 แหล่งอ้างอิง
- [GitHub CLI Manual](https://cli.github.com/manual/)
- [GitHub CLI Cheat Sheet](https://github.github.io/cli/)
### 2.3 GitHub API (REST & GraphQL)
#### 🔹 REST API
- *ตัวอย่าง*: ดึงข้อมูล Repository
  ```bash
  curl -H "Authorization: token YOUR_TOKEN" \
    https://api.github.com/repos/wat-69/My-first-project
  ```
#### 🔹 GraphQL API
- *ตัวอย่าง*: ดึง Pull Requests
  ```graphql
  query {
    repository(owner: "wat-69", name: "My-first-project") {
      pullRequests(states: OPEN) {
        nodes {
          title
          url
          author {
            login
          }
        }
      }
    }
  }
  ```
- เรียกใช้ผ่าน `curl`:
  ```bash
  curl -X POST \
    -H "Authorization: bearer YOUR_TOKEN" \
    -H "Content-Type: application/json" \
    -d '{"query": "{ repository(owner: \"wat-69\", name: \"My-first-project\") { name } }"}' \
    https://api.github.com/graphql
  ```
> 💡 **Tip**: 
> **<span style="color:#F39C12">GraphQL เร็วกว่า REST API เพราะดึงข้อมูลได้ในครั้งเดียว</span>**
#### 📌 แหล่งอ้างอิง
- [GitHub REST API Docs](https://docs.github.com/en/rest)
- [GitHub GraphQL API Docs](https://docs.github.com/en/graphql)
- [GitHub API Explorer](https://docs.github.com/en/graphql/overview/explorer)
### 2.4 GitHub Webhooks
Webhooks ช่วย **ส่ง Event จาก GitHub ไปยัง Server ของคุณ**
#### 🔹 วิธีการตั้งค่า
1. ไปที่: `Settings > Webhooks > Add webhook`
2. ใส่ **Payload URL** (เช่น: `https://your-server.com/webhook`)
3. เลือก **Events** (เช่น: `push`, `pull_request`, `issues`)
4. เลือก **Content Type**: `application/json`
#### 🔹 ตัวอย่าง Payload (Push Event)
```json
{
  "ref": "refs/heads/main",
  "before": "abc123",
  "after": "def456",
  "repository": {
    "name": "My-first-project",
    "full_name": "wat-69/My-first-project"
  },
  "commits": [
    {
      "id": "def456",
      "message": "feat: add new feature",
      "author": {
        "name": "User",
        "email": "user@example.com"
      }
    }
  ]
}
```
#### 🔹 ตัวอย่าง Server (Node.js)
```javascript
const express = require('express');
const app = express();
app.post('/webhook', (req, res) => {
  const payload = req.body;
  console.log('Received event:', payload.action);
  if (payload.action === 'opened' && payload.pull_request) {
    console.log('New PR opened:', payload.pull_request.title);
  }
  res.status(200).send('OK');
});
app.listen(3000, () => console.log('Webhook server running on port 3000'));
```
> ⚠️ **Warning**: 
> **<span style="color:#F39C12">ต้อง Verify Webhook Signature เพื่อป้องกัน Attack!</span>**
> *ตัวอย่าง*: `github-webhook-handler` (Node.js)
#### 📌 แหล่งอ้างอิง
- [GitHub Webhooks Guide](https://docs.github.com/en/webhooks)
- [Webhook Events & Payloads](https://docs.github.com/en/webhooks/webhook-events-and-payloads)
### 2.5 GitHub Codespaces & Copilot
#### 🔹 GitHub Codespaces
- **คือ**: Cloud Development Environment (VS Code ใน Browser)
- **ข้อดี**: 
  - ไม่ต้อง Setup Environment บนเครื่อง
  - ใช้งานได้ทุกที่
  - มี Pre-configured Environments
*ตัวอย่าง*: สร้าง Codespace
```bash
# สร้าง Codespace จาก Repository
gh codespace create --repo wat-69/My-first-project
# เชื่อมต่อกับ Codespace
gh codespace ssh
```
#### 🔹 GitHub Copilot
- **คือ**: AI Pair Programmer (ช่วยเขียนโค้ด)
- **วิธีการใช้งาน**:
  1. ติดตั้ง Extension ใน VS Code
  2. พิมพ์ Comment หรือ Code → Copilot จะแนะนำ
> 💡 **Tip**: 
> **<span style="color:#F39C12">Copilot ใช้งานได้ฟรีสำหรับ Student และ Open Source Projects</span>**
#### 📌 แหล่งอ้างอิง
- [GitHub Codespaces Docs](https://docs.github.com/en/codespaces)
- [GitHub Copilot Docs](https://docs.github.com/en/copilot)
