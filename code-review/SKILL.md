# Code Review Skill

## Two Modes

This skill operates in two different situations:

| Mode | When | What It Does |
|------|------|--------------|
| **A — Pre-Commit Review** | Before commit, self-review | Run checklist, decide if ready to commit |
| **B — Feedback Response** | Someone reviewed, feedback received | Classify each comment, respond, fix |

---

## MODE A: Pre-Commit Review (Self)

### 1. Get Diff and Analyze

```bash
git diff HEAD          # unstaged changes
git diff --staged      # staged changes
git diff main...HEAD   # all branch changes
```

Review every changed file. Fix things you don't want PR comments on now.

---

### 2. Checklist — Apply in Order

#### 🔴 Critical (no commit if these exist)

- [ ] Hardcoded secret, API key, password?
  ```bash
  git grep -i "password|secret|api_key|token" -- '*.ts' '*.js' '*.py' '*.env'
  ```
- [ ] SQL injection vector? (raw query + user input)
- [ ] Authentication or authorization bypass risk?
- [ ] Tests passing?
  ```bash
  npm test
  ```
- [ ] Build breaking?
  ```bash
  npm run build
  ```
- [ ] Breaking change affecting other modules? (notify)

#### 🟡 Important (fix or consciously skip)

- [ ] New public function/endpoint has JSDoc or type?
- [ ] Error handling done? (try-catch, error boundary, 404/500 handling)
- [ ] Debug logs left? (`console.log`, `print`, `debugger`)
- [ ] New env variable added to `.env.example`?
- [ ] Any TODO or FIXME left? (add ticket number if intentional)
- [ ] Code coverage dropped?
  ```bash
  npm test -- --coverage
  ```
- [ ] New dependency added? Security checked?
  ```bash
  npm audit
  ```

#### 🔵 Quality (for perfect code)

- [ ] Function names describe what they do?
- [ ] Any function doing multiple things? (split)
- [ ] Code copied? (copy-paste → extract to function)
- [ ] Magic number or string? (extract to constant)
- [ ] Type usage strict enough? (`any` used?)

---

### 3. Create Review Card

To add to commit message or PR description:

```markdown
## Self-Review Summary

**Change:** <what I did, why>
**Test Status:** ✅ All tests passing / ⚠️ X tests skipped (reason: ...)
**Checklist:**
  - 🔴 Critical: Clean
  - 🟡 Important: [note if any]
  - 🔵 Quality: [note if any]

**Specifically want to draw attention to:**
- <area you want reviewer to look at>

**Deliberate shortcuts (with reason):**
- <if any, with justification>
```

---

## MODE B: Feedback Response (Others' Review)

### 1. Classify Each Comment

For every review comment:

```
[CRITICAL]  → Blocks merge, must fix — fix immediately
[IMPORTANT] → Strong suggestion, fix unless good reason not to
[SUGGESTION] → Discretionary, may need open discussion
[QUESTION]   → Information request — clarify or fix with code
[NIT]        → Small style/naming issue, your preference
```

### 2. Respond to Every Comment

**No comment left unanswered.**

```markdown
Comment: "This function has no timeout, what if service doesn't respond?"
→ Class: [CRITICAL]
→ Response: "You're right. Added 5-second timeout + retry. See T3 commit."
→ Action: Fixed ✅

Comment: "Maybe use Set instead of Map here"
→ Class: [SUGGESTION]
→ Response: "Set is correct here, no need for key. Changed."
→ Action: Fixed ✅

Comment: "This naming doesn't seem semantic to me"
→ Class: [NIT]
→ Response: "Understood the point but `processUserData` → `normalizeUserInput` is clearer — changed."
→ Action: Fixed ✅
```

### 3. Group Fixes and Commit

Commit each fix group separately (`github` skill commit standard):

```bash
# Each fix as separate commit
git commit -m "fix: add timeout and retry to userService.fetchProfile"
git commit -m "refactor: use Set instead of Map in deduplication logic"
git commit -m "refactor: rename processUserData to normalizeUserInput"
```

### 4. Request Re-Review

When all fixes are done, update the review:

```markdown
## Fix Summary

Total comments: 8
- Fixed: 7
- Rejected (with reason): 1 → "NIT comment: current naming aligns with project convention"

Significant changes:
- Timeout + retry mechanism added (security-critical)
- 2 refactors (for clarity)

Ready for re-review.
```

---

## Rules

- **No commit with red items.** No exceptions.
- **"Will fix later" forbidden.** Either fix now or create ticket, add ticket number to code.
- **Every review comment gets response.** "OK" or silence not acceptable — either "fixed" or "disagree because..."
- **Review is not for self-deception.** Don't rush the checklist.
- **Feedback is not personal.** Comments are about code, not you.