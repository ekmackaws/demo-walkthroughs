# Kiro IDE Demo Guide

Step-by-step walkthrough — set up from scratch and demo all five features

## Quick Reference

| Feature | What It Does | Where It Lives |
|---------|-------------|----------------|
| Specs | Plan before coding — requirements, design, tasks | .kiro/specs/ |
| Steering | Persistent project rules the AI always follows | .kiro/steering/ |
| Skills | On-demand expertise packages for specific jobs | .kiro/skills/ |
| Hooks | Automated reactions to IDE events | Hooks panel in sidebar |
| Subagents | Parallel workers with separate context windows | .kiro/agents/ |

---

## Step 1: Steering — Set the Rules First

> 🎤 *Steering files give Kiro persistent knowledge about our project — our stack, conventions, and structure. Instead of repeating ourselves every chat, we set the rules once.*

### Setup

1. Open the **Steering** section in the Kiro side panel
2. Click **Generate Steering Docs**
3. Kiro scans the project and creates three files:
   - `product.md` — what the product is
   - `tech.md` — stack, tools, conventions
   - `structure.md` — project layout and naming
4. Open `.kiro/steering/tech.md` and show the audience the contents

### Test It

> 💬 `Write a script that lists the top 5 most invoked Lambda functions this week`

✅ **Expect:** Bash script, uses `python3 -c` for JSON parsing, defaults to `us-east-1`, uses `<function-name>` style placeholders, uses AWS CLI commands

### Prove It (Optional Before/After)

1. Rename `.kiro/steering` to `.kiro/steering-backup`
2. Open a **new chat** and ask the same prompt
3. Compare — without steering, Kiro may use `jq`, different region, different placeholder style
4. Rename back to `.kiro/steering`

> 💡 **Tip:** Four inclusion modes: **always** (default), **fileMatch** (pattern-based), **manual** (on-demand), **auto** (Kiro decides by relevance)

---

## Step 2: Specs — Plan Before You Code

> 🎤 *Instead of just prompting and getting code, Specs make Kiro plan first — requirements, design, then tasks. We review and approve at each phase before any code gets written.*

### Create a Spec

1. Open the **Specs** section in the Kiro panel, click **+**
2. Enter this prompt:

> 💬 `Build a script that diagnoses Lambda failures in the last hour`

3. Select **Feature** when asked
4. Choose **Requirements-First** workflow
5. Kiro generates `requirements.md` — review the EARS notation requirements
6. Approve (or edit first) → Kiro generates `design.md`
7. Approve → Kiro generates `tasks.md`

### Show the Artifacts

Open each file and point out:

- **requirements.md** — structured WHEN/SHALL statements, edge cases you didn't think of
- **design.md** — architecture, which AWS services to call, data flow
- **tasks.md** — ordered implementation steps

### Edit a Requirement (Optional)

Open `requirements.md` and change one — e.g., add:

```
WHEN the function has zero invocations
THE SYSTEM SHALL report "no activity" but still check for recent log streams
```

This shows the audience they're in control, not the AI.

### Run the Tasks

1. Click a task to execute it — Kiro writes actual code
2. In supervised mode, review each file change before accepting
3. Or run all tasks sequentially

✅ **Expect:** Generated code follows your steering rules (bash, python3 -c, us-east-1) because steering + specs work together

---

## Step 3: Hooks — Automate Reactions

> 🎤 *Hooks are "if this happens, do that" automations. We'll set one up that logs every spec task's changes to a text file automatically.*

### Create a Hook

1. Go to **Agent Hooks** in the Kiro side panel
2. Click **+**
3. Select **Ask Kiro to create a hook**
4. Type:

> 💬 `Create a copy of the changes performed in a txt file each time a spec task step is executed`

5. Review the generated config — should be a **Post Task Execution** trigger
6. Click **Save Hook**

### Test It

1. Go back to your spec and run a task
2. After it completes, the hook fires automatically
3. Open `changes_log.txt` (or whatever file it creates)
4. Show the audience the logged changes

✅ **Expect:** Changes are logged without you doing anything — the hook fired automatically after the spec task completed

### Available Triggers

| Trigger | When It Fires |
|---------|--------------|
| File Save / Create / Delete | When matching files change |
| Prompt Submit | Before agent processes your message |
| Agent Stop | After agent finishes its turn |
| Pre/Post Tool Use | Before or after agent uses a tool |
| Pre/Post Task Execution | Before or after a spec task runs |
| Manual | You invoke it via / slash command |

### Action Types

- **Ask Kiro** — sends a prompt to the agent (uses credits)
- **Run Command** — executes a shell command (free)

---

## Step 4: Skills — On-Demand Expertise

> 🎤 *Skills are portable instruction packages. They only activate when your request matches the skill's description — like a runbook Kiro pulls off the shelf when needed.*

### Create a Skill

Create this folder and file: `.kiro/skills/troubleshoot-lambda/SKILL.md`

With this content:

```markdown
---
name: troubleshoot-lambda
description: Diagnose Lambda timeout, memory, and permission failures. Use when debugging Lambda issues.
---

## Steps

1. Check CloudWatch duration and memory metrics
2. Pull the last 10 error log entries from CloudWatch Logs
3. Verify the execution role has required permissions
4. Check if the function hit concurrency limits
```

### Test It

> 💬 `My Lambda function is throwing timeout errors, help me diagnose it`

✅ **Expect:** Kiro activates the skill and follows the 4 steps in order instead of freestyling

> 💡 **Tip:** Skills can also be invoked manually — type **/** in chat to see available skills as slash commands

### Skills vs Steering

| | Steering | Skills |
|---|---------|--------|
| When it loads | Always (or conditionally) | Only when request matches |
| Purpose | Project rules and conventions | Specific workflow instructions |
| Analogy | Style guide on the wall | Runbook pulled off the shelf |
| Portable | Kiro-specific | Open standard (works in other tools) |

---

## Step 5: Subagents — Parallel Workers with Separate Context

> 🎤 *Subagents let Kiro delegate tasks to separate agents that run in parallel. Each one has its own context window, so the main agent doesn't get overloaded. It's about speed and keeping context clean.*

### Built-in Subagents

- **Context gatherer** — explores your project and collects relevant info
- **General-purpose** — handles parallelized tasks

### Custom Subagent Setup

Create a markdown file in `.kiro/agents/` (workspace) or `~/.kiro/agents/` (global).

Example `.kiro/agents/aws-investigator.md`:

```markdown
---
name: aws-investigator
description: Investigate AWS Lambda and CloudWatch issues
  by gathering logs, metrics, and configuration details.
  Use when diagnosing AWS service problems.
tools: ["read", "shell"]
---

You are an AWS infrastructure investigator.

## Your Responsibilities
- Query CloudWatch metrics and logs for Lambda functions
- Check IAM role permissions and configurations
- Identify error patterns and root causes
- Report findings in a clear, structured format
```

### Test It — Automatic Selection

> 💬 `Investigate why my Lambda functions in us-east-1 have been throwing errors this week`

✅ **Expect:** Kiro matches the request to the aws-investigator description and delegates to it automatically

### Test It — Explicit Invocation

> 💬 `Use the aws-investigator subagent to check CloudWatch metrics for my Lambda functions`

✅ **Expect:** Kiro explicitly launches the aws-investigator subagent

### Test It — Slash Command

Type **/aws-investigator** in chat followed by your request.

### Test It — Parallel Execution

> 💬 `Run subagents to check CloudWatch errors for three Lambda functions: auth-handler, payment-processor, and notification-sender`

✅ **Expect:** Kiro launches multiple subagents in parallel, each investigating a different function, then combines results

### Key Attributes

| Attribute | What It Does | Example |
|-----------|-------------|---------|
| name | Agent name (required) | aws-investigator |
| description | When to use this agent | Investigate AWS Lambda issues |
| tools | What tools it can access | ["read", "shell"] |
| model | Which AI model to use | claude-sonnet-4 |
| includeMcpJson | Include all MCP tools | true |

### Tool Access Options

- **read** — file read tools
- **write** — file write tools
- **shell** — shell command tools
- **web** — web search/fetch tools
- **@builtin** — all built-in tools
- **@\<mcp_server\>** — tools from a specific MCP server
- **\*** — everything

### How Subagents Differ from Steering & Skills

| | Steering | Skills | Subagents |
|---|---------|--------|-----------|
| What it is | Rules | Workflow instructions | A separate worker |
| Purpose | Tell the agent *how* to behave | Tell the agent *what steps* to follow | Offload work to a parallel agent |
| Context | Loaded into main agent | Loaded into main agent | Own separate context window |
| When to use | Always-on conventions | Specific job needs a runbook | Heavy tasks that would overload one agent |

> 💡 **Tip:** Steering files still apply inside subagents. Hooks do NOT fire inside subagents. Subagents cannot access Specs.

---

## Demo Narrative (Tying It All Together)

> 🎤 *Steering sets the rules for the project. Specs plan the feature before any code is written. The agent executes the tasks, and hooks automatically log what changed. When I need a specific workflow — like troubleshooting — a skill kicks in with a defined process. When the work is too heavy for one agent, subagents split it up and run in parallel. All five features work together without extra prompting.*

### Recommended Demo Order

1. **Steering** — generate docs, show the rules, test with a prompt
2. **Specs** — create a spec, walk through requirements → design → tasks
3. **Hooks** — create a hook, run a spec task, show the auto-logged changes
4. **Skills** — create a skill, trigger it with a matching prompt
5. **Subagents** — create a custom agent, show parallel execution

### Time Estimate

| Feature | Setup | Demo |
|---------|-------|------|
| Steering | ~2 min (generate docs) | ~3 min |
| Specs | ~3 min (generate all phases) | ~6 min |
| Hooks | ~1 min (create hook) | ~3 min |
| Skills | ~1 min (create SKILL.md) | ~3 min |
| Subagents | ~1 min (create .md file) | ~3 min |

**Total: ~26 minutes**


