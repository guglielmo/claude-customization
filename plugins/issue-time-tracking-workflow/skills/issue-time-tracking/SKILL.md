---
name: issue-time-tracking
description: Use when user says "start working on issue #N" or "stop working on issue #N" - automates synchronized issue status tracking and time logging across GitLab/GitHub and TMetric with strict single-timer enforcement
---

# Issue Time Tracking Workflow

## Overview

Automates issue status and time tracking across platforms. Single command starts timer, updates status. Single command stops timer, logs time, adds comment.

**Core principle**: One active timer only. Always block if timer running. No exceptions.

## When to Use

**Triggers**:
- "start working on issue #123"
- "stop working on #456"
- "stop working on #789 - fixed the authentication bug"
- "pause timer" / "resume timer"
- "what am I working on?"

**Don't use for**:
- Manual time logging without timer
- Bulk issue updates
- Issue creation/deletion

## Announcing Skill Usage

**REQUIRED**: Before executing any workflow, announce:

"I'm using the issue-time-tracking skill to [start work on/stop work on/check status of] issue #N."

This confirms skill is active and helps user understand the workflow being followed.

## Platform Auto-Detection

Auto-detect GitLab vs GitHub from git remote:
1. Check `git remote -v` in current repo
2. If contains `gitlab.com` or GitLab domain → use `glab`
3. If contains `github.com` → use `gh`
4. Cache result for session

If ambiguous → ask once: "Which platform? (gitlab/github)"

## TMetric Project Mapping

**First time in repository**:
1. Check: `git config tmetric.project-id`
2. If not found:
   - Call `list_tmetric_projects()`
   - Analyze repo context (path, remote URL)
   - Rank likely matches at top, show all others
   - User selects by number
   - Save: `git config tmetric.project-id <chosen-id>`
3. Future sessions: Use saved value

## Start Working Workflow

**Command**: "start working on issue #123"

**Steps**:
1. ✅ **Check active timer FIRST**: `get_current_timer()`
2. ⛔ **If timer exists → BLOCK immediately**:
   ```
   ❌ Cannot start. Timer already running on issue #X (Xh Ym elapsed).
   Stop that timer first with: 'stop working on issue #X'
   ```
   **STOP HERE. Do not proceed.**

3. If no timer:
   - Detect platform (glab/gh)
   - Verify issue exists
   - Get TMetric project (check config → rank suggestions → save choice)
   - Start timer: `start_timer(project_id, "Issue #123: [title]", url)`
   - Update issue status:
     - GitLab: `glab issue update 123 -u "status::*" -l "status::in progress"`
     - GitHub: Try project status field → fallback to labels → add comment

4. **If partial failure** (timer started but status update failed):
   ```
   Timer started (running on [Project]) but issue status update failed.
   Error: [details]
   Should I stop the timer? (yes/no)
   ```
   - Yes → stop timer
   - No → leave running, explain manual fix needed

**Success**: "✓ Started work on issue #123. Timer running on [Project]. Status: in progress"

## Stop Working Workflow

**Command**: "stop working on issue #456" or "stop working on #456 - completed auth flow"

**Steps**:
1. **Extract summary** from command if present (after dash)
2. If no summary → ask: "What work did you complete?"
3. Stop timer: `stop_timer()` (returns elapsed time)
4. Add comment with time:
   - GitLab: `glab issue comment 456 -m "Work completed: [summary]\n\n/spend 2h30m"`
   - GitHub: `gh issue comment 456 -b "Work completed: [summary]\n\nTime spent: 2h 30m"`
5. Update status:
   - GitLab: Remove `status::*`, add `status::testing`
   - GitHub: Update via project/labels + comment

**Success**: "✓ Completed work on issue #456. Time logged: 2h 30m. Status: testing"

## Single Timer Enforcement

**THE IRON LAW**: Only ONE active timer allowed. Always.

### Blocking Rule

Before starting new timer:
1. ALWAYS call `get_current_timer()`
2. If timer active → BLOCK with error message
3. Force user to explicitly stop current timer
4. NEVER auto-stop, even if:
   - User says "urgent"
   - User says "production down"
   - Current timer has run for hours
   - User seems frustrated
   - Timer is on different project
   - User added "please" or "now"

### Red Flags - STOP and Block

If you think ANY of these, you are rationalizing. Block anyway:
- "This is an emergency, auto-stop is justified"
- "Asking wastes the user's cognitive energy"
- "Urgent command implies permission to stop timer"
- "Being helpful means removing friction"
- "30 seconds delay is worse than helping"
- "User has authority to override the rule"

**All of these mean**: Block with error message. No exceptions.

### Why No Auto-Stop?

| Rationalization | Reality |
|-----------------|---------|
| "Production emergency justifies it" | User might want to log partial time first. Block. |
| "Asking wastes cognitive energy" | Losing 2h of time data is worse. Block. |
| "Urgent = implicit permission" | No. User can stop explicitly. Block. |
| "Being helpful" | Protecting time data IS helpful. Block. |

**Even under maximum pressure**: Block. Explain. User stops explicitly.

## Other Workflows

### Pause Timer
"pause timer" → `pause_timer()` → no status change

### Resume Timer
"resume timer" → `resume_timer()`

### Check Status
"what am I working on?" → `get_current_timer()` → display task, elapsed

## Error Handling

**Principle**: Always ask before rolling back.

### Timer Started, Issue Update Failed
```
Timer started but status update failed.
Error: [details]
Should I stop the timer? (yes/no)
```

### Timer Stopped, Issue Update Failed
```
Timer stopped (2h 30m logged) but couldn't update issue.
Error: [details]
Should I retry? (yes/no)
```

**Always include**:
- What succeeded
- What failed
- Current state
- Suggested action or manual recovery

## Status Label Conventions

**GitLab**: `status::in progress`, `status::testing`, `status::review`, `status::done`
**GitHub**: `status: in progress`, `status: testing`, `status: review`, `status: done`

Always remove ALL status labels before adding new one (only one active).

## Common Mistakes

### ❌ Auto-stopping on urgency
```
User: "start on #222 urgent!!!"
Agent: [stops current timer automatically]
```
**Fix**: Block with error showing current timer. Always.

### ❌ Not checking timer first
```
Agent: [starts new timer without checking]
TMetric: Error - timer already running
```
**Fix**: Call `get_current_timer()` BEFORE attempting start.

### ❌ Asking for summary when provided
```
User: "stop working on #123 - fixed bug"
Agent: "What work did you complete?"
```
**Fix**: Parse summary after dash. Only ask if missing.

### ❌ Auto-rolling back on partial failure
```
[Timer starts, status update fails]
Agent: [stops timer automatically]
```
**Fix**: Ask user. They might want timer to keep running.
