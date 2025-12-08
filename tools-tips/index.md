---
layout: doc
title: Tools & Productivity
---

# 🛠️ Tools & Productivity

Tài liệu về công cụ, tricks, shortcuts và workflow để tăng productivity khi code.

## 📚 Overview

Section này tập trung vào **tools và techniques** giúp developers làm việc hiệu quả hơn:

- **Git**: Version control workflows và commands
- **VSCode**: Editor configuration, extensions, shortcuts
- **Terminal**: Bash/Zsh tips, command-line tools
- **Workflow**: Code review, debugging, best practices

---

## 🗂️ Content Structure

### 🌿 Git - Version Control

Essential tool cho mọi developer.

**Topics:**
- [Common Commands](/tools-tips/git/common-commands) - Daily Git commands
- [Git Workflows](/tools-tips/git/workflows) - Feature branch, GitFlow
- [Troubleshooting](/tools-tips/git/troubleshooting) - Fix common issues
- [Advanced Git](/tools-tips/git/advanced-git) - Rebase, cherry-pick, reflog

**Key Concepts:**
- Working directory, staging area, repository
- Branches & merging
- Remote repositories (origin, upstream)
- Commit history & messages
- Pull requests / Merge requests
- Conflict resolution

**Daily Commands:**
```bash
git status          # Check status
git add .           # Stage changes
git commit -m "msg" # Commit
git push            # Push to remote
git pull            # Pull from remote
```

---

### 💻 VSCode - Code Editor

Powerful, extensible code editor.

**Topics:**
- [Essential Extensions](/tools-tips/vscode/extensions) - Must-have extensions
- [Keyboard Shortcuts](/tools-tips/vscode/shortcuts) - Speed up workflow
- [Settings & Config](/tools-tips/vscode/settings) - Optimize VSCode
- [Snippets](/tools-tips/vscode/snippets) - Code templates

**Key Features:**
- IntelliSense (autocomplete)
- Integrated terminal
- Git integration
- Debugging
- Extensions marketplace
- Multi-cursor editing

**Must-have Extensions:**
- ESLint - Linting
- Prettier - Code formatting
- GitLens - Git supercharged
- Path Intellisense - Path autocomplete
- Auto Rename Tag - HTML/JSX tag sync

---

### 🖥️ Terminal - Command Line

Master the terminal cho efficient workflow.

**Topics:**
- [Bash Tricks](/tools-tips/terminal/bash-tricks) - Bash shortcuts & tips
- [Zsh Setup](/tools-tips/terminal/zsh-setup) - Oh My Zsh configuration
- [Terminal Tools](/tools-tips/terminal/terminal-tools) - Useful CLI tools

**Key Concepts:**
- Shell basics (bash, zsh)
- Navigation (cd, ls, pwd)
- File operations (cp, mv, rm, mkdir)
- Pipes & redirection
- Environment variables
- Shell scripting basics

**Useful Tools:**
- `fzf` - Fuzzy finder
- `ripgrep (rg)` - Fast search
- `bat` - Better cat
- `exa` - Better ls
- `tldr` - Simplified man pages
- `httpie` - HTTP client

---

### 🔄 Development Workflow

Best practices cho team collaboration.

**Topics:**
- [Code Review Tips](/tools-tips/workflow/code-review) - Effective reviews
- [Debugging Techniques](/tools-tips/workflow/debugging) - Find & fix bugs

**Key Concepts:**

**Code Review:**
- Pull request best practices
- Review checklist
- Constructive feedback
- Automated checks

**Debugging:**
- Console methods (log, table, trace)
- Browser DevTools
- Debugger statements
- Network inspection
- Performance profiling

---

## 🎯 Quick Wins

### Git Aliases

**~/.gitconfig:**
```bash
[alias]
    st = status
    co = checkout
    br = branch
    ci = commit
    unstage = reset HEAD --
    last = log -1 HEAD
    lg = log --oneline --graph --decorate
```

**Usage:**
```bash
git st        # Instead of git status
git co main   # Instead of git checkout main
git lg        # Pretty log
```

---

### VSCode Shortcuts (Must Know)

**General:**
- `Cmd/Ctrl + P` - Quick open file
- `Cmd/Ctrl + Shift + P` - Command palette
- `Cmd/Ctrl + B` - Toggle sidebar
- `Cmd/Ctrl + J` - Toggle terminal

**Editing:**
- `Cmd/Ctrl + D` - Select next occurrence
- `Alt + Click` - Multi-cursor
- `Cmd/Ctrl + /` - Toggle comment
- `Alt + ↑↓` - Move line up/down
- `Shift + Alt + ↑↓` - Copy line up/down

**Navigation:**
- `Cmd/Ctrl + Shift + F` - Search in files
- `Cmd/Ctrl + G` - Go to line
- `F12` - Go to definition
- `Shift + F12` - Find references

---

### Terminal Shortcuts

**Navigation:**
```bash
Ctrl + A    # Beginning of line
Ctrl + E    # End of line
Ctrl + U    # Clear line before cursor
Ctrl + K    # Clear line after cursor
Ctrl + W    # Delete word before cursor
Ctrl + R    # Search command history
```

**Process:**
```bash
Ctrl + C    # Stop running command
Ctrl + Z    # Suspend command
Ctrl + D    # Exit shell
```

---

## 🚀 Quick Start

### Git Setup

```bash
# Configure Git
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"

# Set default branch name
git config --global init.defaultBranch main

# Enable color output
git config --global color.ui auto

# Set default editor
git config --global core.editor "code --wait"
```

---

### VSCode Setup

**Install VSCode:**
- Download từ [code.visualstudio.com](https://code.visualstudio.com/)
- Install command line tools: `Cmd/Ctrl + Shift + P` → "Shell Command: Install 'code' command"

**Essential Extensions:**
```bash
# Install via command line
code --install-extension dbaeumer.vscode-eslint
code --install-extension esbenp.prettier-vscode
code --install-extension eamodio.gitlens
code --install-extension formulahendry.auto-rename-tag
code --install-extension christian-kohler.path-intellisense
```

**Settings JSON:**
```json
{
  "editor.formatOnSave": true,
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "editor.tabSize": 2,
  "editor.minimap.enabled": false,
  "files.autoSave": "afterDelay",
  "terminal.integrated.fontSize": 14
}
```

---

### Terminal Setup (Zsh + Oh My Zsh)

**Install Zsh:**
```bash
# macOS (already installed)
zsh --version

# Linux
sudo apt-get install zsh
```

**Install Oh My Zsh:**
```bash
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

**Configure (~/.zshrc):**
```bash
# Theme
ZSH_THEME="robbyrussell"

# Plugins
plugins=(
  git
  node
  npm
  docker
  vscode
  zsh-autosuggestions
  zsh-syntax-highlighting
)
```

---

## 💡 Best Practices

### Git Commit Messages

```bash
# ✅ Good - Clear, descriptive
git commit -m "feat: add user authentication"
git commit -m "fix: resolve login redirect issue"
git commit -m "docs: update API documentation"

# ❌ Bad - Vague
git commit -m "update"
git commit -m "fix bug"
git commit -m "wip"
```

**Conventional Commits:**
- `feat:` - New feature
- `fix:` - Bug fix
- `docs:` - Documentation
- `style:` - Formatting, no code change
- `refactor:` - Code restructuring
- `test:` - Adding tests
- `chore:` - Maintenance tasks

---

### Branch Naming

```bash
# ✅ Good - Descriptive naming
git checkout -b feature/user-auth
git checkout -b fix/login-redirect
git checkout -b refactor/api-endpoints

# ❌ Bad - Unclear
git checkout -b new-branch
git checkout -b temp
git checkout -b test123
```

**Patterns:**
- `feature/description` - New features
- `fix/description` - Bug fixes
- `hotfix/description` - Production fixes
- `refactor/description` - Code refactoring
- `docs/description` - Documentation

---

### Code Organization (VSCode)

```javascript
// ✅ Good - Use regions for folding
// #region Imports
import React from 'react';
import { useState } from 'react';
// #endregion

// #region Components
function MyComponent() {
  // ...
}
// #endregion

// ❌ Bad - No organization
import React from 'react';
function MyComponent() {}
import { useState } from 'react';
```

---

### Terminal Aliases

**~/.zshrc or ~/.bashrc:**
```bash
# Navigation
alias ..="cd .."
alias ...="cd ../.."
alias ~="cd ~"

# Git shortcuts
alias gs="git status"
alias ga="git add"
alias gc="git commit -m"
alias gp="git push"
alias gl="git pull"
alias gco="git checkout"

# Development
alias ni="npm install"
alias ns="npm start"
alias nt="npm test"
alias nb="npm run build"

# Docker
alias dc="docker-compose"
alias dcu="docker-compose up -d"
alias dcd="docker-compose down"
alias dps="docker ps"
```

---

## 📖 Recommended Topics

### For Git

1. **Start here:** [Common Commands](/tools-tips/git/common-commands)
2. **Collaboration:** [Git Workflows](/tools-tips/git/workflows)
3. **Fix issues:** [Troubleshooting](/tools-tips/git/troubleshooting)
4. **Level up:** [Advanced Git](/tools-tips/git/advanced-git)

### For VSCode

1. **Setup:** [Essential Extensions](/tools-tips/vscode/extensions)
2. **Speed up:** [Keyboard Shortcuts](/tools-tips/vscode/shortcuts)
3. **Customize:** [Settings & Config](/tools-tips/vscode/settings)
4. **Templates:** [Snippets](/tools-tips/vscode/snippets)

### For Terminal

1. **Basics:** [Bash Tricks](/tools-tips/terminal/bash-tricks)
2. **Setup:** [Zsh Setup](/tools-tips/terminal/zsh-setup)
3. **Tools:** [Terminal Tools](/tools-tips/terminal/terminal-tools)

### For Workflow

1. **Reviews:** [Code Review Tips](/tools-tips/workflow/code-review)
2. **Debug:** [Debugging Techniques](/tools-tips/workflow/debugging)

---

## 🔗 External Resources

### Documentation
- [Git Docs](https://git-scm.com/doc)
- [VSCode Docs](https://code.visualstudio.com/docs)
- [Oh My Zsh](https://ohmyz.sh/)

### Cheatsheets
- [Git Cheatsheet](https://education.github.com/git-cheat-sheet-education.pdf)
- [VSCode Shortcuts](https://code.visualstudio.com/shortcuts/keyboard-shortcuts-macos.pdf)

---

## 📊 Essential Toolkit

```
Developer Toolkit:
├── Version Control: Git + GitHub/GitLab
├── Code Editor: VSCode
├── Terminal: iTerm2/Hyper + Zsh
├── API Testing: Postman/Insomnia
├── Database GUI: TablePlus/DBeaver
├── HTTP Client: Httpie/curl
├── Search: ripgrep/fzf
└── Documentation: Dash/DevDocs
```

---

## 🎯 Next Steps

1. **Learn Git?** → [Common Commands](/tools-tips/git/common-commands)
2. **Setup VSCode?** → [Essential Extensions](/tools-tips/vscode/extensions)
3. **Improve Terminal?** → [Zsh Setup](/tools-tips/terminal/zsh-setup)
4. **Better Workflow?** → [Code Review](/tools-tips/workflow/code-review)

---

## 💡 Pro Tips

### Productivity Hacks

1. **Learn keyboard shortcuts** - 2x faster coding
2. **Use Git aliases** - Less typing
3. **Master terminal** - GUI is slower
4. **Automate repetitive tasks** - Write scripts
5. **Use snippets** - Pre-written code templates

### Time Savers

- `Ctrl + R` trong terminal - Search command history
- Multiple cursors trong VSCode - Edit nhiều dòng cùng lúc
- Git stash - Save work without committing
- VSCode Workspaces - Group related projects
- Terminal tabs/splits - Multiple sessions

---

**Work smarter, not harder! ⚡**

[← DevOps](/devops/) | [Về trang chủ](/)