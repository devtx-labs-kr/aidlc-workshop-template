# AI-DLC Sync Agent - Developer Guide

## What This Agent Does

The AI-DLC Sync Agent helps you stay synchronized with your team during the Construction Phase. It handles the tedious work of pulling changes, understanding what changed, and keeping your AI-DLC documentation up to date.

## Quick Start

### Activate the Agent

**Option 1: Keyboard Shortcut**
```
Press: Ctrl+Shift+S
```

**Option 2: Slash Command**
```
/agent swap
→ Select: aidlc-sync-agent
```

**Option 3: CLI Direct Start**
```bash
kiro-cli --agent aidlc-sync-agent
```

---

## Real-World Example: Morning Sync Workflow

### Scenario
You're working on the `user-authentication` unit in a team project. You left work yesterday after completing the login API. This morning, you need to sync with changes your teammates pushed overnight.

### Step-by-Step Usage

#### 1. Start Your Day - Activate the Agent

```
[You press Ctrl+Shift+S]

🔄 AI-DLC Sync Agent ready! I'll help you pull changes, analyze diffs, 
and update your AI-DLC documentation. What would you like to sync?

[Agent automatically runs git status]
On branch feature/user-auth
Your branch is behind 'origin/feature/user-auth' by 3 commits.
  (use "git pull" to update your local branch)

Changes not staged for commit:
  modified:   src/auth/login.ts
```

#### 2. Request a Sync

**You say:**
```
Sync my branch. I have some local changes in login.ts that aren't committed yet.
```

**Agent responds:**
```
I see you have uncommitted changes in src/auth/login.ts. Let me help you 
sync safely.

**Current Status:**
- Local changes: src/auth/login.ts (modified, not staged)
- Remote: 3 new commits on origin/feature/user-auth

**Recommended approach:**
1. Stash your local changes
2. Pull the 3 new commits
3. Pop your stash back
4. Resolve any conflicts if needed

Should I proceed with this approach?
```

#### 3. Confirm and Execute

**You say:**
```
Yes, go ahead
```

**Agent executes:**
```bash
# Stashing your changes...
git stash push -m "WIP: login.ts changes before sync"

# Fetching latest...
git fetch origin

# Pulling changes...
git pull --no-rebase

# Restoring your work...
git stash pop
```

**Agent reports:**
```
🔄 **Sync Complete**

**Pull Summary:**
- Commits pulled: 3
- Files changed: 7 files (+245/-89 lines)

**Changes from teammates:**
1. `src/auth/register.ts` - Sarah added email verification
2. `src/auth/middleware.ts` - Mike updated JWT validation  
3. `src/models/user.ts` - Sarah added emailVerified field
4. `tests/auth/*.test.ts` - 3 new test files
5. `aidlc-docs/construction/user-authentication/` - Updated designs

**Your stashed changes:**
✅ Applied cleanly to src/auth/login.ts (no conflicts)

**Impact on your work:**
⚠️  The User model changed - you may need to update login.ts 
    to handle the new emailVerified field
```

#### 4. Review AI-DLC Documentation Updates

**Agent continues:**
```
**AI-DLC Documentation Updates:**

I've updated the following files:

1. **aidlc-docs/aidlc-state.md**
   - Added sync event record
   - Updated unit status for user-authentication

2. **aidlc-docs/audit.md**
   - Logged sync operation with timestamp
   - Recorded all file changes

3. **aidlc-docs/construction/user-authentication/code/**
   - Updated register-api-summary.md (new email verification)
   - Updated user-model-summary.md (new field)

**Next Steps:**
1. Review the User model changes in src/models/user.ts
2. Update your login.ts to check emailVerified status
3. Run tests: npm test -- --grep "auth"
```

---

## Common Use Cases

### Use Case 1: Simple Daily Sync
```
You: "Pull latest changes"

Agent: 
- Checks for local changes
- Pulls remote commits
- Shows what changed
- Updates AI-DLC docs
```

### Use Case 2: Sync with Conflicts
```
You: "Sync my branch"

Agent:
- Detects merge conflicts
- Shows both versions of conflicting code
- Proposes resolution strategies
- Asks for your decision on each conflict
- Updates docs after resolution
```

### Use Case 3: Pre-PR Sync
```
You: "I'm about to create a PR. Make sure I'm up to date with main"

Agent:
- Fetches from main branch
- Shows divergence
- Helps merge main into your branch
- Updates AI-DLC docs to reflect merged changes
```

### Use Case 4: Understanding Team Changes
```
You: "What did the team change in the last week?"

Agent:
- Runs git log for past week
- Categorizes changes by unit/component
- Summarizes impact on AI-DLC documentation
- Highlights changes relevant to your current work
```

---

## Example Conversation: Handling Merge Conflicts

```
You: "Sync with origin"

Agent: 
⚠️ **Merge Conflict Detected**

Conflicting files:
1. src/auth/middleware.ts

**Conflict in middleware.ts (lines 45-62):**

<<<<<<< HEAD (your changes)
const validateToken = (token: string) => {
  return jwt.verify(token, process.env.JWT_SECRET);
};
=======
const validateToken = async (token: string) => {
  const decoded = jwt.verify(token, process.env.JWT_SECRET);
  await checkTokenBlacklist(decoded.jti);
  return decoded;
};
>>>>>>> origin/feature/user-auth (incoming)

**Analysis:**
- Your version: Synchronous token validation
- Incoming version: Async validation with blacklist check (Mike's security update)

**Recommendation:** Accept incoming changes - the blacklist check is a 
security improvement. Your sync validation still works with the async version.

How would you like to resolve this?
A) Accept incoming (recommended)
B) Keep yours
C) Let me merge both manually
D) Show me more context

You: "A"

Agent:
✅ Resolved: Accepted incoming changes for middleware.ts
   Running: git add src/auth/middleware.ts

Continuing merge... Done!

Updated aidlc-docs/audit.md with conflict resolution record.
```

---

## Tips for Best Results

1. **Commit or stash before syncing** - The agent handles uncommitted changes, but it's cleaner to commit your work first

2. **Be specific about branches** - Say "sync with main" or "pull from origin/develop" for clarity

3. **Ask about changes first** - Say "what's new on origin?" before pulling to preview changes

4. **Use for documentation sync** - Even if you manually pulled, ask the agent to "update AI-DLC docs for recent changes"

5. **Review the audit log** - Check `aidlc-docs/audit.md` to see the history of all sync operations

---

## Keyboard Shortcuts

| Shortcut | Action |
|----------|--------|
| `Ctrl+Shift+S` | Toggle AI-DLC Sync Agent |
| `/agent swap` | Switch between agents |
| `/agent list` | See all available agents |

---

## Troubleshooting

**Agent can't find aidlc-docs/**
- Make sure you're in a project with AI-DLC initialized
- The agent creates entries even if the directory doesn't exist yet

**Git commands failing**
- Check your git credentials
- Ensure you have network access to the remote

**Conflicts not resolving**
- The agent never auto-resolves without your approval
- For complex conflicts, choose option D to see more context
