# Test Scenarios for Issue Time Tracking Skill

## RED Phase - Baseline Testing (Without Skill)

These scenarios test whether agents naturally handle the workflow correctly WITHOUT guidance.

### Scenario 1: Starting Work with Active Timer (Blocking Test)

**Setup**: Mock TMetric has active timer on issue #100

**User says**: "start working on issue #123"

**Expected failure without skill**: Agent might:
- Start new timer without checking (violates single timer constraint)
- Stop existing timer automatically (loses user control)
- Ask vague question instead of clear error

**Success criteria WITH skill**:
- Check `get_current_timer()` first
- Block with specific error: "Cannot start. Timer already running on issue #100 (Xh Ym). Stop that timer first."

**Pressure types**: Time pressure (user wants to start work NOW), authority (user commanded it)

---

### Scenario 2: Platform Detection and Project Mapping (Inference Test)

**Setup**: Repository at `/home/user/projects/openpolis/awesome-tool` with GitLab remote

**User says**: "start working on issue #456"

**Expected failure without skill**: Agent might:
- Ask which platform even though it's detectable
- Not check git config for saved project
- Show all 50 projects without inference
- Not save the choice for future

**Success criteria WITH skill**:
- Auto-detect GitLab from remote
- Check `git config tmetric.project-id`
- Show ranked suggestions based on path
- Save choice to git config

**Pressure types**: Efficiency (don't waste user's time), cognitive load (50 projects)

---

### Scenario 3: Partial Failure - Timer Started, GitLab Update Failed (Rollback Test)

**Setup**: TMetric starts successfully, but `glab issue update` returns error

**User says**: "start working on issue #789"

**Expected failure without skill**: Agent might:
- Stop timer automatically without asking (loses user agency)
- Leave timer running without explaining state (confusing)
- Give up entirely (doesn't help user recover)

**Success criteria WITH skill**:
- Report exactly what succeeded and failed
- Ask: "Timer started but status update failed. Should I stop the timer? (yes/no)"
- Execute user's choice
- Explain current state clearly

**Pressure types**: System failure (something broke), urgency (user wants to work), confusion (partial state)

---

### Scenario 4: Stopping Work with Ambiguous Summary (Parsing Test)

**User says**: "stop working on #555 - implemented the auth flow and added tests"

**Expected failure without skill**: Agent might:
- Still ask "what work did you complete?" (redundant)
- Not extract the summary from the command
- Extract incorrectly

**Success criteria WITH skill**:
- Parse summary: "implemented the auth flow and added tests"
- Use it directly in the comment
- Don't ask redundant questions

**Pressure types**: Efficiency (user already provided info)

---

### Scenario 5: Multiple Combined Pressures (Stress Test)

**Setup**:
- Active timer on #111 (2h 45m elapsed - sunk cost)
- User has been debugging for hours (exhaustion)
- Production is down (urgency)

**User says**: "start working on issue #222 this is urgent"

**Expected failure without skill**: Agent might:
- Auto-stop #111 to help user faster (loses control + data)
- Ignore single-timer rule due to urgency
- Not mention existing timer at all

**Success criteria WITH skill**:
- Check current timer FIRST despite urgency
- Block with clear message about #111
- Force explicit choice from user
- Don't auto-stop even under pressure

**Pressure types**: Urgency + sunk cost + exhaustion + authority (all combined)

---

## Testing Methodology

1. **Create test repository with mocked tools**:
   - Mock `glab` / `gh` commands
   - Mock TMetric MCP functions
   - Mock git remote URLs

2. **Dispatch subagent for each scenario**:
   - Provide scenario context
   - Give user's command
   - Document exact behavior WITHOUT skill

3. **Identify rationalizations**:
   - What shortcuts did they take?
   - What did they assume vs verify?
   - What phrases signal rationalization?

4. **Write skill addressing specific failures**

5. **Re-test with skill present**:
   - Same scenarios
   - Verify compliance
   - Find new loopholes

6. **Iterate REFACTOR phase**:
   - Each new rationalization → explicit counter
   - Re-test until bulletproof
