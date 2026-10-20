# Writing Plans Skill

## Prerequisite

Before starting this skill, these must be ready:
- [ ] `brainstorming` completed and scope doc exists → `.agent/SCOPE-<slug>.md`
- [ ] Success criteria are clear
- [ ] Tech stack decision is made

**Load the scope doc:** Search for `.agent/SCOPE-*.md` pattern and read the most recent one.
If these don't exist, run `brainstorming` skill first.

---

## Process

### Step 1: Map Affected Areas

Scan the existing codebase and determine what will change:

```
src/
├── components/     ← New component: UserCard
├── services/       ← Changed: userService.ts
├── api/            ← New endpoint: POST /api/users
├── db/migrations/  ← New migration: add_users_table
└── tests/          ← New tests: userService.test.ts
```

For each affected file:
- **New, changing, or deleting?**
- **What depends on this file?** (can it be changed without breaking?)

---

### Step 2: Break Tasks into Atomic Pieces

Each task:
- **Does one thing**
- **Can be independent commit**
- **Is testable** (how do you know it's complete?)
- **Takes < 2 hours** (if longer, split)

```markdown
## Task List

### T1: Database migration — users table
- File: db/migrations/001_create_users.sql
- Content: id, email, password_hash, created_at, deleted_at
- Dependency: None (first step)
- Test: Migration up/down works

### T2: User model and repository
- File: src/db/repositories/userRepository.ts
- Content: findById, findByEmail, create, softDelete
- Dependency: T1 (migration must exist)
- Test: repository unit tests pass

### T3: UserService — business logic
- File: src/services/userService.ts
- Content: createUser (hash password), getUser, deleteUser
- Dependency: T2
- Test: service unit tests pass

### T4: API endpoint — POST /api/users
- File: src/api/routes/users.ts
- Content: validation, auth middleware, service call
- Dependency: T3
- Test: integration test passes

### T5: Frontend — UserCard component
- File: src/components/UserCard/
- Content: component + test + storybook
- Dependency: T4 (API mock or real)
- Test: component tests pass
```

---

### Step 3: Draw Dependency Graph

```
T1 (migration)
  └── T2 (repository)
        └── T3 (service)
              └── T4 (API endpoint)
                    └── T5 (frontend)
```

**Mark tasks that can run in parallel** as separate branches:
```
T1
├── T2 → T3 → T4 (backend branch)
└── T5-mock (frontend branch — can start with mocked API)
```

---

### Step 4: Flag Risks and Checkpoints

For each task:
- `[RISK]` — if it fails, others are blocked
- `[CHECKPOINT]` — can pause here, results can be verified
- `[BREAKING]` — breaks existing code, needs attention
- `[PARALLEL]` — can be done simultaneously with another task

```markdown
### T3: UserService [RISK] [CHECKPOINT]
This task forms the foundation of all backend. Should pause here for review before proceeding to API layer.
```

---

### Step 5: Write the Plan

Write to `implementation_plan.md`:

```markdown
# Implementation Plan: <project name>
**Date:** <YYYY-MM-DD>  
**Estimated Time:** <X days/hours>
**Source:** [Scope Doc](.agent/SCOPE-<slug>.md)

## Summary
<2-3 sentences: what will be done, how>

## Tech Stack Decisions
| Area | Choice | Rationale |
|------|-------|-----------|
| ORM | Prisma | Type-safe, good migration management |
| Auth | JWT + refresh token | Stateless, scalable |

## Tasks

### 🔵 Phase 1: Foundation (Cannot run in parallel)
- [ ] **T1:** Database migration `[CHECKPOINT]`
- [ ] **T2:** Repository layer

### 🟡 Phase 2: Core (After T1 and T2)
- [ ] **T3:** Service layer `[RISK][CHECKPOINT]`
- [ ] **T4:** API endpoint `[BREAKING]`

### 🟢 Phase 3: UI (When T4 or mock is ready)
- [ ] **T5:** Frontend component `[PARALLEL]`

## Risk List
| Risk | Likelihood | Impact | Mitigation |
|------|------------|--------|------------|
| Irreversible migration | Low | High | Write and test down migration |

## Undefined Points
- [ ] Email verification flow: needed? (decision pending)

## Next Step
1. Run `architecture-review` skill — no coding starts without plan approval
2. After approval, `executing-plans` or `dispatching-parallel-agents`
```

---

## Rules

- **Every task ends with test criteria.** "Complete" = tests pass.
- **No task contains "and also".** If it does, split.
- **"We'll handle later" doesn't go in plan.** Either make it a task or move out of scope.
- **If plan changes, update it.** Old plan becomes invalid — don't delete, add date.
- **`architecture-review` cannot be skipped.** Especially required if any `[RISK]` tasks exist.