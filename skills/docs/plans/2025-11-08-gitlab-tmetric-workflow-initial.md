# GitLab + TMetric Development Workflow

## Overview

This skill enables automated time tracking and issue management workflow for developers using GitLab issues and TMetric time tracking. It coordinates actions between GitLab (via `glab` CLI) and TMetric (via MCP server) to maintain synchronized state across both systems.

**Core principle**: Keep GitLab issues and TMetric timers synchronized. If any operation fails, stop immediately and report the error. Partial state is worse than no state.

## Available Tools

### GitLab (via glab CLI)
- `glab issue view <number>` - Read issue details
- `glab issue update <number>` - Update issue (labels, status)
- `glab issue comment <number>` - Add comments to issues
- Use `bash_tool` to execute glab commands

### TMetric (via MCP server)
- `list_tmetric_projects()` - Get list of available projects
- `start_timer(project_id, task_name, task_url?)` - Start time tracking
- `stop_timer()` - Stop current timer and get time spent
- `get_current_timer()` - Check if timer is running
- `pause_timer()` - Pause current timer (e.g., for lunch break)
- `resume_timer()` - Resume paused timer

## Workflows

### 1. Starting Work on an Issue

When the user says "start work on issue #X" or similar:

**Steps:**

1. **Read the issue**
   ```bash
   glab issue view <number>
   ```
   - Verify the issue exists and is accessible
   - Note the issue title and project context

2. **Check for active timers**
   - Call `get_current_timer()` - this queries TMetric API for any active timer
   - If a timer is already running, **DO NOT start new timer**
   - Inform user: "Cannot start timer on Issue #X. Timer already running on [task] (started [time], elapsed [duration]). Please stop that timer first."
   - **IMPORTANT**: Never auto-stop existing timers. User must explicitly stop them.

3. **Get TMetric projects**
   - Call `list_tmetric_projects()`
   - Projects returned as: `[{id: 1, name: "Project Name"}, ...]`

4. **Select project**
   - Try to infer from issue context:
     - Issues in `openpolis/*` repos → "Openpolis" or "Fondazione Openpolis" project
     - Issues in `depp/*` repos → "Depp SRL" project
   - If unclear, ask user explicitly: "Which TMetric project should I use: [list projects]?"

5. **Start the timer**
   - Call `start_timer(project_id, task_name, task_url)`
   - `task_name` format: "Issue #X: [issue title]"
   - `task_url`: Full GitLab issue URL (e.g., https://gitlab.com/group/project/-/issues/123)
   - Check response `success` field

6. **Update GitLab labels**
   ```bash
   glab issue update <number> --remove-label "status::*" --add-label "status::progress"
   ```
   - This removes ALL labels starting with "status::" and adds "status::progress"

**Success message:**
"✓ Started work on issue #X. Timer running on [project]. GitLab status: progress"

---

### 2. Completing Work on an Issue

When the user says "finish work on issue #X", "done with #X", or similar:

**Steps:**

1. **Verify current timer**
   - Call `get_current_timer()`
   - Verify it's tracking the correct issue
   - If no timer running, inform user and ask if they want to proceed anyway

2. **Get work summary from user**
   - Ask: "What work did you complete on issue #X?"
   - This will be added to the GitLab comment

3. **Stop the timer**
   - Call `stop_timer()`
   - Response should include `time_spent` (e.g., "2h 30m")
   - Check response `success` field

4. **Add comment to GitLab**
   ```bash
   glab issue comment <number> --message "Work completed: [user's summary]

/spend [time_spent]"
   ```
   - Format time as GitLab expects (e.g., "2h30m", "1h", "45m")
   - The `/spend` is a GitLab quick action that logs time

5. **Update GitLab status**
   ```bash
   glab issue update <number> --remove-label "status::*" --add-label "status::testing"
   ```

**Success message:**
"✓ Completed work on issue #X. Time logged: [time]. Status: testing"

---

### 3. Pausing Work (Lunch, Break)

When the user says "pause timer", "lunch break", etc:

**Steps:**

1. **Check current timer**
   - Call `get_current_timer()`
   - Verify a timer is running

2. **Pause the timer**
   - Call `pause_timer()`
   - Check response `success` field

**Note:** GitLab issue status remains "status::progress" during pauses.

**Success message:**
"⏸ Timer paused on [task]"

---

### 4. Resuming Work

When the user says "resume timer", "back from lunch", etc:

**Steps:**

1. **Resume the timer**
   - Call `resume_timer()`
   - Check response `success` field

**Success message:**
"▶ Timer resumed on [task]"

---

### 5. Checking Current Status

When the user asks "what am I working on?", "current timer?", etc:

**Steps:**

1. **Get current timer**
   - Call `get_current_timer()`
   - Display task name, project, and duration

**Example response:**
"Currently tracking: Issue #123: Fix login bug (Openpolis project) - 1h 23m elapsed"

## Error Handling - CRITICAL

**NEVER proceed if any step fails.** Stop immediately and inform the user with the exact error.

### Starting work errors:

- **glab issue view fails** → Stop. Report: "Issue #X not found or not accessible"
- **list_tmetric_projects() fails** → Stop. Report: "Cannot access TMetric. Check connection/authentication"
- **start_timer() returns success: false** → Stop. Do NOT update GitLab. Report the error message from TMetric
- **GitLab label update fails** → Try to stop the timer that was started (rollback). Report: "Timer started but GitLab update failed. Timer stopped. Please try again."

### Completing work errors:

- **stop_timer() fails** → Stop. Do NOT update GitLab. Report: "Cannot stop TMetric timer. Please check TMetric and stop manually, then update GitLab manually."
- **Cannot retrieve time_spent** → Stop. Report: "Timer stopped but cannot get time spent. Please check TMetric and add time to GitLab manually with: glab issue comment X --message '/spend Xh'"
- **GitLab comment/update fails** → Timer is stopped but issue not updated. Report: "Timer stopped ([time]) but GitLab update failed. Please manually add comment and update status."

### General error principles:

- **Do not guess or assume** - If information is missing, ask the user
- **Do not try workarounds** - If TMetric is down, don't say "I'll just update GitLab"
- **Report exact errors** - Include error messages from tools
- **Suggest recovery** - Tell user what they need to do manually if automation failed

### Example error messages:

❌ "Cannot connect to TMetric API. Error: Connection timeout. Please check your network and TMetric authentication."

❌ "Issue #123 not found. Verify the issue number and your access permissions."

❌ "Timer started successfully but GitLab label update failed. I've stopped the timer. Error: glab returned status 1. Please try again."

## Important Notes

### Single Timer Limitation:
- **TMetric allows only ONE active timer at a time**
- This limitation is enforced across ALL Claude Code sessions
- If you try to start work on Issue #456 while Issue #123's timer is running, you'll get an error
- You must explicitly stop the current timer before starting a new one
- This prevents accidental time tracking overlap and keeps your time logs clean

### Time format conversion:
- TMetric might return time in various formats
- GitLab `/spend` expects: "1h", "30m", "2h30m", "1.5h"
- Convert if necessary

### Status label conventions:
- Only ONE status::* label should be active at a time
- Always remove ALL status::* labels before adding new one
- Valid status labels: status::progress, status::testing, status::review, status::done

### Project inference rules:
- Be conservative in inferring projects
- When in doubt, ask the user
- Remember user's choice for similar issues in the same conversation (but don't persist across sessions)

### Multiple issues:
- Users might switch between issues
- Always verify which issue they're referring to
- When starting new work, remind if another issue is in progress

## Usage Examples

### Example 1: Simple start/finish flow

```
User: Start work on issue #456

Claude: Let me start work on that issue...
[Reads issue, gets projects, starts timer, updates labels]
✓ Started work on issue #456: "Optimize database queries". 
Timer running on Openpolis project. GitLab status: progress

[... later ...]

User: Done with #456

Claude: What work did you complete on issue #456?

User: Optimized the main query, added indexes, reduced load time by 50%

Claude: Stopping timer and updating GitLab...
✓ Completed work on issue #456. Time logged: 2h 15m. Status: testing
```

### Example 2: Handling ambiguity

```
User: Start work on issue #789

Claude: Let me check issue #789...
[Reads issue: "Update documentation"]
I see this is for the "shared-tools" repository. Which TMetric project 
should I track this under?
1. Openpolis
2. Depp SRL

User: Openpolis

Claude: Starting timer on Openpolis...
✓ Started work on issue #789. Timer running on Openpolis. Status: progress
```

### Example 3: Lunch break

```
User: Pause timer, going to lunch

Claude: ⏸ Timer paused on Issue #456: Optimize database queries

[... 1 hour later ...]

User: Resume

Claude: ▶ Timer resumed on Issue #456: Optimize database queries (was paused for 58m)
```

### Example 4: Error handling

```
User: Start work on issue #999

Claude: Let me start work on that issue...
[Calls glab issue view 999]
❌ Issue #999 not found. Verify the issue number and your access permissions.

[OR]

Claude: Starting timer on Openpolis...
[start_timer fails]
❌ Cannot start TMetric timer. Error: Authentication failed. 
Please check your TMetric API credentials.
GitLab has NOT been updated.
```

## Skill Development Notes

This skill is designed to work with:
- **glab CLI** - Already configured and working
- **TMetric MCP server** (minimal) - Provides only the 5 operations needed

The TMetric MCP server should be kept minimal and focused. Complex TMetric operations (project management, task editing, historical data) should be done in the TMetric web interface.

## Future Enhancements

Possible additions (document here as you discover needs):
- Automatic project inference based on repository path patterns
- Support for issue branches (create branch when starting work)
- Integration with merge request workflow
- Weekly time tracking summaries
- Bulk operations (finish all testing issues)

---

**Version**: 1.0  
**Last updated**: 2025-11-08  
**Status**: Initial draft - to be refined during implementation
