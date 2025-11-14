# Issue Time Tracking Workflow Skill - Design Document

**Date**: 2025-11-10
**Status**: Design Complete - Ready for Implementation

## Overview

A Claude Code skill that automates issue status tracking and time logging across GitLab/GitHub and TMetric. Users can say "start working on issue #123" or "stop working on issue #123" and have Claude coordinate all necessary actions across platforms.

## Goals

- **Single command workflow**: Start/stop work with simple natural language
- **Platform agnostic**: Auto-detect GitLab vs GitHub, handle differences transparently
- **Synchronized state**: Keep issue status and time tracking in sync
- **Smart but safe**: Infer project mappings intelligently, but always confirm with user
- **Explicit error handling**: Ask user before rolling back partial operations

## Architecture

### Platform Detection

Auto-detect whether to use `glab` or `gh` CLI:
1. Check git remote URLs in current repository
2. If remote contains `gitlab.com` or self-hosted GitLab domain → use `glab`
3. If remote contains `github.com` → use `gh`
4. Cache detection result for conversation session
5. If ambiguous or missing → ask user once

### Tool Integration

- **GitLab CLI**: `glab` (pre-configured, authenticated)
- **GitHub CLI**: `gh` (pre-configured, authenticated)
- **TMetric MCP Server**: Provides 6 operations:
  - `list_tmetric_projects()` - Get available projects
  - `start_timer(project_id, task_name, task_url)` - Start tracking
  - `stop_timer()` - Stop and return time spent
  - `get_current_timer()` - Check current state
  - `pause_timer()` - Pause for breaks
  - `resume_timer()` - Resume after break
- **Git**: Repository context and configuration storage

### TMetric Project Mapping

**Storage**: Use git config (local to clone): `git config tmetric.project-id <id>`

**Selection Flow**:
1. First time in repo: Check `git config tmetric.project-id`
2. If not found:
   - Get repo context (remote URL, name, path, README)
   - Call `list_tmetric_projects()` to get all ~50 projects
   - Use LLM to analyze and rank likely matches
   - Present ranked list to user:
     ```
     Likely matches:
     1. Fondazione Openpolis (based on 'openpolis/' in path)
     2. Depp SRL (based on similar patterns)

     Other projects:
     3. Project Alpha
     4. Project Beta
     ...
     ```
   - User selects by number
   - Save to `git config tmetric.project-id <chosen-id>`
3. Future sessions: Use saved config value

**Rationale**: Git config is local-only (never pushed), provides per-repo persistence, and uses standard git tooling.

### GitHub Status Tracking Strategy

**Challenge**: GitHub lacks GitLab's built-in `status::*` label system.

**Solution - Hybrid Approach**:

1. **Try GitHub Projects first** (most native):
   - Check if issue in project: `gh issue view <n> --json projectItems`
   - Update status field or move between columns
   - GitHub Projects v2 have native "Status" field

2. **Fallback to labels**:
   - Use labels: `status: in progress`, `status: testing`
   - Similar to GitLab but with space instead of `::`
   - Command: `gh issue edit <n> --remove-label "status: *" --add-label "status: in progress"`

3. **Always add comment** (audit trail):
   - Timestamped regardless of projects/labels
   - "Started work at HH:MM" / "Completed at HH:MM (Xh Ym)"

**Graceful degradation**: Projects → Labels → Comments only

### Single Timer Enforcement

**Constraint**: TMetric allows only ONE active timer at a time.

**Strategy**: Strict blocking (no auto-switching)
- Always check `get_current_timer()` before starting new work
- If timer exists → report which issue, elapsed time, and block
- Error message: "Cannot start. Timer already running on issue #X (Xh Ym elapsed). Stop that timer first."
- User must explicitly stop current work before starting new work

**Rationale**: Prevents accidental time tracking overlap and data loss.

## Core Workflows

### Workflow 1: Start Working

**Trigger**: "start working on issue #123"

**Steps**:
1. **Detect platform** (glab vs gh)
2. **Read issue** to verify exists
3. **Check active timers** - if running → BLOCK with error
4. **Get TMetric project**:
   - Check git config
   - If not found → show ranked suggestions → save choice
5. **Start timer**: `start_timer(project_id, "Issue #123: [title]", url)`
   - If fails → STOP, report error
6. **Update issue status**:
   - GitLab: `glab issue update <n> --remove-label "status::*" --add-label "status::in progress"`
   - GitHub: Try project → fallback labels → add comment
   - If fails → Ask: "Timer started but status update failed. Stop the timer? (yes/no)"

**Success**: "✓ Started work on issue #123. Timer running on [Project]. Status: in progress"

### Workflow 2: Stop Working

**Trigger**: "stop working on issue #123" or "stop working on #123 - fixed the bug"

**Steps**:
1. **Verify timer** (optional check)
   - Warn if no timer or wrong issue
2. **Extract work summary**:
   - If included in command → extract it
   - If not → ask: "What work did you complete?"
3. **Stop timer**: `stop_timer()`
   - Returns time_spent (e.g., "2h 30m")
   - If fails → STOP, report: "Cannot stop timer. Please stop manually."
4. **Add comment with time**:
   - GitLab: `glab issue comment <n> -m "Work completed: [summary]\n\n/spend 2h30m"`
   - GitHub: `gh issue comment <n> -b "Work completed: [summary]\n\nTime spent: 2h 30m"`
   - If fails → Ask: "Timer stopped (2h 30m) but comment failed. Retry?"
5. **Update status to testing**:
   - GitLab: Remove `status::*`, add `status::testing`
   - GitHub: Update via project/labels

**Success**: "✓ Completed work on issue #123. Time logged: 2h 30m. Status: testing"

### Workflow 3: Pause Timer

**Trigger**: "pause timer" / "going to lunch"

**Steps**:
1. Check current timer exists
2. Call `pause_timer()`
3. No issue status change (remains "in progress")

**Success**: "⏸ Timer paused on issue #123: [title]"

### Workflow 4: Resume Timer

**Trigger**: "resume timer" / "back from lunch"

**Steps**:
1. Call `resume_timer()`

**Success**: "▶ Timer resumed on issue #123: [title]"

### Workflow 5: Check Status

**Trigger**: "what am I working on?" / "current timer?"

**Steps**:
1. Call `get_current_timer()`
2. Display task, project, elapsed time

**Example**: "Currently tracking: Issue #123: Fix login bug (Fondazione Openpolis) - 1h 23m elapsed"

### Workflow 6: Switching Issues (Blocked)

**Trigger**: "start working on #456" while #123 is active

**Response**: "❌ Cannot start. Timer already running on issue #123 (1h 23m elapsed). Stop that timer first with: 'stop working on issue #123'"

## Error Handling

**Principle**: Always ask user before rolling back partial operations.

### Partial Failure Scenarios

**1. Timer started, issue update failed**:
```
Timer started (running on [Project]) but issue status update failed.
Error: [details]
Should I stop the timer? (yes/no)
```
- Yes → stop timer, clean state
- No → leave running, explain manual fix

**2. Timer stopped, issue update failed**:
```
Timer stopped (2h 30m logged) but couldn't update issue.
Error: [details]
Should I retry? (yes/no)
```
- Timer already stopped, just need issue sync
- Provide manual commands if retries fail

**3. Platform detection fails**:
```
Cannot detect if this is GitLab or GitHub.
Which platform? (gitlab/github)
```

**4. Project inference unclear**:
- Show all 50 projects as numbered list
- Let user choose by number

### Error Message Format

Always include:
- What succeeded
- What failed
- Current state
- Suggested next action or manual recovery

**Example**: "❌ Issue #123 not found. Verify the issue number and try: `glab issue list`"

## Implementation Notes

### GitHub Time Tracking Limitation

GitHub has no native time tracking quick actions like GitLab's `/spend`. The skill will:
- Add time as plain text in comments: "Time spent: 2h 30m"
- Consider future enhancement: Use GitHub issue custom fields if available

### Status Label Conventions

**GitLab**: `status::in progress`, `status::testing`, `status::review`, `status::done`
**GitHub**: `status: in progress`, `status: testing`, `status: review`, `status: done`

Always remove ALL status labels before adding new one (only one active at a time).

### Session Context

The skill should remember within a conversation:
- Platform detection result (gitlab vs github)
- Current timer state
- User's project selection patterns (if similar repos)

Does NOT persist across sessions:
- Timer state (query TMetric each time)
- Project mappings (stored in git config, not conversation)

## Future Enhancements

Possible additions to document as needs arise:
- Automatic branch creation when starting work
- Integration with merge request workflow
- Weekly time tracking summaries
- Bulk operations (finish all testing issues)
- Support for GitHub issue custom fields for time tracking

## Success Criteria

The skill is successful if:
1. User can start/stop work with single natural language command
2. Issue status and time tracking stay synchronized across platforms
3. Errors are transparent and recoverable
4. Project mapping is learned once per repository
5. No accidental timer overlaps or lost time data

---

**Next Steps**: Use `superpowers:writing-skills` to implement this design as a Claude Code skill.
