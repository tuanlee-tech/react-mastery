---
layout: doc
title: Getting Started - DSA Mastery
---

# 🚀 Getting Started with DSA Mastery

Chuẩn bị cho hành trình 100 ngày biến đổi career của bạn!

---

## 📋 Prerequisites

### Kiến thức cần có:
- ✅ **JavaScript basics**: Variables, functions, loops, conditionals
- ✅ **Programming logic**: Hiểu control flow cơ bản
- ✅ **Problem-solving attitude**: Sẵn sàng học từ mistakes

### Kiến thức KHÔNG cần:
- ❌ Computer Science degree
- ❌ Experience với DSA
- ❌ Advanced programming skills
- ❌ Math degree

> **Note:** Nếu bạn chưa biết JavaScript, học basic JavaScript trước (1-2 tuần) rồi quay lại.

---

## 🛠️ Setup Environment

### 1. Install Node.js

**macOS:**
```bash
brew install node
```

**Windows:**
- Download từ [nodejs.org](https://nodejs.org/)
- Install LTS version (v20+)

**Verify:**
```bash
node --version  # Should show v20+
npm --version   # Should show v10+
```

---

### 2. Code Editor

**Recommended: VS Code**
- Download từ [code.visualstudio.com](https://code.visualstudio.com/)

**Essential Extensions:**
```bash
# Install via command line
code --install-extension dbaeumer.vscode-eslint
code --install-extension esbenp.prettier-vscode
code --install-extension formulahendry.code-runner
```

**Or install manually:**
- ESLint - Linting
- Prettier - Code formatting
- Code Runner - Run code quickly

---

### 3. Create Accounts

**LeetCode** (Primary platform)
1. Go to [leetcode.com](https://leetcode.com)
2. Sign up (free account)
3. Explore problems

**HackerRank** (Secondary)
1. Go to [hackerrank.com](https://hackerrank.com)
2. Sign up
3. Complete initial assessment

---

## 📁 Project Structure

### Setup your workspace:

```bash
# Create main folder
mkdir dsa-100-days
cd dsa-100-days

# Create structure
mkdir -p {phase-1,phase-2,phase-3,phase-4}/{week-1,week-2,week-3,week-4}
mkdir -p practice solved notes

# Initialize Node.js project
npm init -y

# Install helpful packages
npm install chalk
```

**Your structure should look like:**
```
dsa-100-days/
├── phase-1/
│   ├── week-1/
│   ├── week-2/
│   ├── week-3/
│   └── week-4/
├── phase-2/
├── phase-3/
├── phase-4/
├── practice/       # Daily practice problems
├── solved/         # Completed solutions
├── notes/          # Your notes and learnings
└── package.json
```

---

## 📝 Create Your First File

**practice/template.js:**
```javascript
/**
 * Problem: [Problem Name]
 * Link: [LeetCode/HackerRank URL]
 * Difficulty: Easy/Medium/Hard
 * Date: YYYY-MM-DD
 */

// ===== PROBLEM STATEMENT =====
/*
Write problem description here
*/

// ===== EXAMPLES =====
/*
Example 1:
Input: 
Output: 
Explanation:

Example 2:
Input:
Output:
*/

// ===== APPROACH =====
/*
1. Brute Force:
   - Time: O(?)
   - Space: O(?)

2. Optimized:
   - Time: O(?)
   - Space: O(?)
   
3. Optimal:
   - Time: O(?)
   - Space: O(?)
*/

// ===== SOLUTION 1: BRUTE FORCE =====
function solution1() {
  // Implementation
}

// ===== SOLUTION 2: OPTIMIZED =====
function solution2() {
  // Implementation
}

// ===== SOLUTION 3: OPTIMAL =====
function solution3() {
  // Implementation
}

// ===== TEST CASES =====
console.log('Test 1:', solution3());
console.log('Test 2:', solution3());
```

---

## 🗓️ Study Schedule

### Daily Commitment: 2-3 hours

**Ideal Schedule:**
```
Morning (1 hour):
└─ Review theory + watch explanations

Afternoon/Evening (1.5-2 hours):
├─ Solve Problem 1 (Easy)
├─ Solve Problem 2 (Medium)
└─ Attempt Problem 3 (Hard)

Before Bed (15-30 mins):
└─ Review solutions + note key patterns
```

**Weekend:**
- Review entire week
- Re-solve difficult problems
- Participate in LeetCode contests

---

## 📊 Progress Tracking

### Create a Spreadsheet

**Columns:**
- Day #
- Date
- Topic
- Problems Solved
- Time Spent
- Difficulty
- Notes
- Need Review?

**Template:**
| Day | Date | Topic | Problems | Time | Notes |
|-----|------|-------|----------|------|-------|
| 1 | 2024-01-01 | Big O | 3 | 2.5h | Clear concept |
| 2 | 2024-01-02 | Arrays | 3 | 3h | Two pointers! |

---

## 🎯 Success Metrics

### Track these weekly:

**Problems Solved:**
- [ ] Easy: X problems
- [ ] Medium: X problems
- [ ] Hard: X problems

**Concepts Mastered:**
- [ ] Topic 1
- [ ] Topic 2
- [ ] Topic 3

**Patterns Learned:**
- [ ] Pattern 1
- [ ] Pattern 2

---

## 💡 Study Tips

### 1. **Before Starting:**
- [ ] Read problem 2-3 times thoroughly
- [ ] Understand constraints
- [ ] Identify edge cases
- [ ] Think of brute force first

### 2. **While Solving:**
- [ ] Write pseudocode first
- [ ] Test with examples manually
- [ ] Optimize step by step
- [ ] Comment your code

### 3. **After Solving:**
- [ ] Analyze time/space complexity
- [ ] Compare with other solutions
- [ ] Note patterns used
- [ ] Add to review list

---

## 🚫 Common Mistakes to Avoid

### ❌ Don't:
1. **Jump to code immediately** - Think first!
2. **Give up after 10 minutes** - Struggle is learning
3. **Copy solutions without understanding** - Defeats purpose
4. **Skip easy problems** - Build foundation
5. **Ignore complexity analysis** - Critical for interviews

### ✅ Do:
1. **Understand the "why"** behind solutions
2. **Practice explaining** your approach
3. **Review regularly** (1 week, 2 weeks, 1 month)
4. **Join study groups** for motivation
5. **Celebrate small wins** 🎉

---

## 🔗 Useful Resources

### Visualization Tools:
- [VisuAlgo](https://visualgo.net) - Visualize algorithms
- [Algorithm Visualizer](https://algorithm-visualizer.org)
- [Data Structure Visualizations](https://www.cs.usfca.edu/~galles/visualization/)

### References:
- [LeetCode Patterns](https://seanprashad.com/leetcode-patterns/)
- [Blind 75](https://www.teamblind.com/post/New-Year-Gift---Curated-List-of-Top-75-LeetCode-Questions-to-Save-Your-Time-OaM1orEU)

### Communities:
- r/leetcode on Reddit
- LeetCode Discuss section
- Discord servers for competitive programming

---

## ✅ Pre-Start Checklist

Before beginning Day 1:

- [ ] Node.js installed and verified
- [ ] VS Code setup with extensions
- [ ] LeetCode account created
- [ ] Project structure created
- [ ] Template file ready
- [ ] Progress tracker setup
- [ ] 2-3 hours blocked daily
- [ ] Study group formed (optional)
- [ ] Motivation at 100% 🔥

---

## 🎊 You're Ready!

Everything is setup. Time to start your journey!

**Next Step:** [Phase 1 - Week 1: Complexity Analysis](/dsa/phase-1/week-1)

---

**Remember:** 
> "The expert in anything was once a beginner who refused to give up."

**Let's go! 🚀**

[← Back to DSA Overview](/dsa/) | [Start Week 1 →](/dsa/phase-1/week-1)