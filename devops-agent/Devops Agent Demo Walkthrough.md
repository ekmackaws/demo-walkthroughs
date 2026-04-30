# 🚀 AWS DevOps Agent — Demo Walkthrough

**Date:** April 9, 2026 | **Estimated Total Time:** 25–30 minutes

## 📋 Table of Contents

1. [Pre-Demo Setup (Do This First)](#-pre-demo-setup-do-this-before-the-demo)
2. [What is AWS DevOps Agent?](#-what-is-aws-devops-agent)
3. [Step 1: DevOps Center — Topology](#step-1-devops-center--topology-2-min)
4. [Step 2: Investigation #1 — Without Skills](#step-2-investigation-1--without-skills-7-min)
5. [Step 3: Upload the Skill](#step-3-upload-the-skill-2-min)
6. [Step 4: Investigation #2 — With Skills](#step-4-investigation-2--with-skills-7-min)
7. [Step 5: Compare the Results](#step-5-compare-the-results-3-min)
8. [Step 6: Chat Demo](#step-6-chat-demo-3-min)
9. [Step 7: Prevention](#step-7-prevention-1-min)
10. [Reference: Key Concepts](#-reference-key-concepts-if-asked)
11. [Cleanup](#-post-demo-cleanup)

---

## 🔧 Pre-Demo Setup (Do This BEFORE the Demo)

> ⚠️ **Do these steps at least 30 minutes before the demo starts.**

### 1. Create a New Agent Space

1. Go to **AWS DevOps Agent** in the AWS Management Console
2. Click **Create Agent Space +**
3. Name it (e.g., "demo")
4. Under "Give this Agent Space AWS resource access" → select **Auto-create a new AWS DevOps Agent role**
5. Under "Enabling the Agent Space Web App" → select **Auto-create a new AWS DevOps Agent role**
6. Click **Submit**
7. Wait for the "Admin access" button to appear

### 2. Deploy the EC2 Stress Test Stack

1. Go to **CloudFormation** → **Create stack** → **With new resources**
2. Upload `ec2stressagent.yaml` from your devopsagent directory
3. Stack name: `AWS-DevOpsAgent-EC2-Test`
4. Check **"I acknowledge that AWS CloudFormation might create IAM resources"**
5. Click **Submit** and wait for `CREATE_COMPLETE` (~3-5 min)

### 3. Run the Stress Test

1. Go to **EC2 console** → find `AWS-DevOpsAgent-Test-Instance`
2. Click **Connect** → **Session Manager** → **Connect**
3. Run:

```bash
sudo bash /home/ec2-user/cpu-stress-test.sh
```

4. Wait ~8-10 min for CloudWatch alarm `AWS-DevOpsAgent-EC2-CPU-Test` to go to "In alarm" state
5. Verify in **CloudWatch** → **Alarms** → should show "In alarm"

### 4. Verify Web App Access

1. Go back to your Agent Space → click **"Admin access"**
2. Confirm the web app opens successfully

> ⚠️ Admin access sessions are limited to **10 minutes**. You may need to re-authenticate mid-demo.

### Pre-Demo Checklist

- [ ] Agent Space created and showing "Admin access" button
- [ ] CloudFormation stack deployed successfully
- [ ] EC2 instance is running
- [ ] Stress test executed
- [ ] CloudWatch alarm is in "In alarm" state
- [ ] Web app opens via "Admin access"
- [ ] Skill text ready to upload (see Step 3 below)

---

## 📖 What is AWS DevOps Agent?

AWS DevOps Agent is a fully managed, AI-powered "frontier agent" that acts as an always-on, autonomous on-call engineer. It:

- **Investigates incidents autonomously** — starts the moment an alert fires
- **Maps your infrastructure** — builds a topology of resources and their relationships
- **Prevents future incidents** — analyzes patterns and recommends improvements
- **Integrates with your tools** — CloudWatch, Datadog, Dynatrace, Splunk, GitHub, GitLab, ServiceNow, Slack, PagerDuty
- **Supports multicloud/hybrid** — via observability tool integrations and MCP servers

**Pricing:** Free during public preview. Limits: 20 investigation hours, 10 prevention hours, 1,000 chat messages/month.

**No infrastructure to manage** — fully serverless. Only IAM roles exist in your account.

### Key Architecture Concepts

| Concept | What It Is |
|---------|-----------|
| **Agent Space** | Logical container defining what the agent can access. |
| **Agent Space Role** | IAM role the agent assumes to investigate AWS resources. |
| **Web App Role** | IAM role humans assume to access the web app. |
| **DevOps Center** | Interactive topology view of resources and relationships. |
| **Skills** | Natural language instructions that teach the agent about your environment. |
| **Capabilities** | Integrations: EKS, CI/CD, MCP servers, multi-account, telemetry, ticketing, webhooks. |
| **Webhook** | HTTP endpoint external tools call to auto-trigger investigations. |
| **Prevention** | Weekly analysis of past investigations to recommend improvements. |

### Chat vs Investigations

| Aspect | Chat | Investigation |
|--------|------|---------------|
| Who drives | You ask, agent answers | Agent runs autonomously |
| Speed | Quick, real-time | Minutes, multi-step |
| Use case | Ad-hoc queries, system health | Full incident response |
| Triggers Prevention | No | Yes |

### Agent Types for Skills

| Agent Type | What It Does |
|-----------|-------------|
| **Generic** | Available to all agents (default) |
| **On-demand** | Chat queries and ad-hoc tasks |
| **Incident Triage** | Initial severity and scope assessment |
| **Incident RCA** | Root cause analysis — the detective |
| **Incident Mitigation** | Generates remediation plans — the fixer |
| **Evaluation** | Prevention — reviews past incidents for recommendations |

---

## Step 1: DevOps Center — Topology (~2 min)

1. Open the web app via **"Admin access"**
2. Click the **"DevOps Center"** tab
3. Walk through the three views: **System** → **Container** → **Resource**

> 💬 *"Before any investigation, the agent has already discovered and mapped all resources and understands how they relate. This topology is what the agent uses during investigations to understand blast radius and trace impact paths."*

---

## Step 2: Investigation #1 — Without Skills (~7 min)

### What to Do

1. Click the **"Incident Response"** tab (or open Chat)
2. Click **"Start Investigation"** or type the prompt in Chat
3. Use the prompt below

### Prompt to Use

```
EC2 instance is experiencing high CPU utilization and a CloudWatch alarm has triggered. Investigate the root cause and determine if this is a critical issue requiring immediate action.
```

### Starting Point

> 💡 **What is a Starting Point?** The first clue you give the agent so it doesn't search blindly. You can type free-form text or pick a pre-configured option.

| Option | What It Does |
|--------|-------------|
| **Latest alarm** | Investigates your most recent triggered CloudWatch alarm |
| **High CPU usage** | Investigates high CPU across compute resources |
| **Error rate spike** | Investigates application error rate increases |

Select **"High CPU usage"** for this demo.

### If the Agent Asks for Instance ID

```
I dont have the instance id at the moment, I know an alarm did trigger for high cpu though
```

> 💡 **While the agent works:** Talk through what it's doing — "It's checking CloudWatch metrics, pulling alarm history, looking at the instance configuration..."

### Expected Results (No Skill)

- Finds the alarm and instance
- Gives a surface-level summary of the CPU spike
- Treats it as a real production issue
- Generic recommendations (scaling, investigate workload)
- Does NOT dig into T-series CPU credit depletion

> 💬 *"The agent found the issue and gave us a summary, but it's treating this generically. It doesn't have deep EC2 diagnostic knowledge. Now let me show you how Skills change that."*

---

## Step 3: Upload the Skill (~2 min)

### What to Do

1. Click the **Settings** icon (gear, top right of web app)
2. Click **"Skills"**
3. Click **"Add skill"**
4. Select **"Upload skill"**
5. Upload the `ec2-instance-diagnostics` skill zip file
6. Leave Agent Type as **Generic** (available to all agents)
7. Click **"Upload"**

> 💡 If creating via UI instead of uploading, use these fields:

**Skill Name:**

```
ec2-instance-diagnostics
```

**Skill Description:**

```
Use this skill to investigate and troubleshoot EC2 instance problems by collecting OS-level diagnostic data and following structured runbooks. Activate when: an instance is unreachable, status checks are failing, performance is degraded (high CPU, memory, disk, or network), SSH/RDP connections fail, EBS volumes won't attach, networking issues, IAM/IMDS failures, boot or initialization failures, or the user says something is wrong with an EC2 instance.
```

> 💬 *"Skills are how you teach the agent about your environment and give it specialized knowledge. They're natural language instructions — like onboarding a new engineer to your on-call rotation. You can target skills to specific agent types: Triage, RCA, Mitigation, or leave it on Generic for all."*

### What are Skills?

- Self-contained Markdown instructions that provide specialized capabilities
- Can include investigation workflows, reference materials, decision trees
- Agent reads the title and description to decide which skill is relevant
- Can be created in the UI or uploaded as a zip file (max 6 MB)
- Active/Inactive toggle to control availability
- Can target specific agent types to reduce context consumption

---

## Step 4: Investigation #2 — With Skills (~7 min)

### What to Do

1. Click **"New session"** in Chat (or start a new investigation from Incident Response)
2. Use the **exact same prompt** as Investigation #1

### Same Prompt

```
EC2 instance is experiencing high CPU utilization and a CloudWatch alarm has triggered. Investigate the root cause and determine if this is a critical issue requiring immediate action.
```

### Starting Point: "High CPU usage"

### If the Agent Asks for Instance ID

```
I dont have the instance id at the moment, I know an alarm did trigger for high cpu though
```

### Follow-Up (When Agent Identifies the Instance)

```
yeah can you check if the credits expired?
```

### Expected Results (With Skill)

- Agent explicitly mentions loading the EC2 diagnostics skill
- Asks about T-series CPU credit exhaustion upfront
- Follows structured triage → deep dive → detailed path workflow
- Builds detailed timeline tables with events
- Identifies historical patterns (previous spikes)
- Pulls actual CPU credit metrics with data proof
- **Correctly identifies root cause: CPU credit depletion on t3.micro**
- Gives specific recommendations: instance sizing, pre-warming, T3 Unlimited cost warning

> 💬 *"Same alarm, same prompt — but the agent now has specialized EC2 diagnostic knowledge. It followed a structured workflow, caught the T-series credit depletion that it completely missed before, and gave us actionable recommendations with data to back them up."*

---

## Step 5: Compare the Results (~3 min)

### Side-by-Side Comparison

| Aspect | Without Skill | With Skill |
|--------|--------------|------------|
| Depth | Surface-level summary | Full diagnostic workflow with metrics |
| T-series awareness | Not mentioned | Flagged credit depletion immediately |
| Root cause | "Looks like a test instance" | CPU credit depletion with data proof |
| Recommendations | Generic (scaling, investigate) | Specific (instance sizing, pre-warming, cost warning) |
| Structure | Conversational | Systematic triage → deep dive |
| Historical analysis | None | Identified recurring pattern |
| Evidence | Alarm data only | CPU credit metrics, timeline tables |

> 💬 *"This is the power of Skills. In production, you'd create skills for your specific architecture — your database troubleshooting playbooks, your deployment rollback procedures, your team's tribal knowledge. The agent goes from a generic troubleshooter to an engineer who knows your systems."*

---

## Step 6: Chat Demo (~3 min)

Click **"New session"** in Chat and try these prompts:

```
What CloudWatch alarms are currently in my account?
```

```
Show me the EC2 instances in my account and their current status.
```

```
What resources would be affected if my EC2 test instance went down?
```

```
What could I improve about my monitoring setup?
```

> 💬 *"Chat lets your ops team query infrastructure in natural language without jumping between consoles. It's context-aware — it knows what page you're on and maintains conversation history for follow-ups."*

---

## Step 7: Prevention (~1 min)

1. Click the **"Prevention"** tab
2. Optionally click **"Run Now"** to trigger an evaluation
3. Explain the feature (may be empty for a new space)

> 💬 *"Over time, as investigations accumulate, the agent analyzes patterns and generates recommendations across four areas: observability, code, infrastructure, and governance. It runs evaluations weekly. You can accept or reject recommendations — when you reject, you tell it why, and it learns from that feedback. Recommendations auto-remove after 6 weeks if no new incidents would have been prevented."*

---

## ⏱️ Quick Reference — Demo Flow

| Step | What | Time |
|------|------|------|
| 1 | DevOps Center — show topology | ~2 min |
| 2 | Investigation #1 — without skill | ~7 min |
| 3 | Upload the skill | ~2 min |
| 4 | Investigation #2 — with skill | ~7 min |
| 5 | Compare results | ~3 min |
| 6 | Chat demo | ~3 min |
| 7 | Mention Prevention | ~1 min |
| | **Total** | **~25 min** |

---

## 📚 Reference: Key Concepts (If Asked)

### Capabilities / Integrations

| Capability | What It Does |
|-----------|-------------|
| AWS EKS Access | Inspect Kubernetes clusters, pod logs, cluster events |
| CI/CD Pipeline Integration | Connect GitHub/GitLab to correlate deployments with incidents |
| MCP Server Connections | Extend with custom tools via Model Context Protocol |
| Multi-Account AWS Access | Add secondary accounts for cross-account investigations |
| Telemetry Sources | Connect Datadog, Dynatrace, New Relic, Splunk |
| Ticketing and Chat | Connect ServiceNow, PagerDuty, Slack |
| Webhooks | Auto-trigger investigations from external tools |

### Webhook + CloudWatch Auto-Trigger Flow

```
CloudWatch Alarm (CPU > 70%)
    ↓ fires
SNS Topic (or EventBridge Rule)
    ↓ HTTPS subscription
DevOps Agent Webhook
    ↓ triggers
Investigation starts automatically
```

Supports HMAC or Bearer token authentication. Any tool that can make an HTTP call can trigger an investigation.

### ServiceNow Integration Flow

1. Incident ticket created in ServiceNow
2. ServiceNow Business Rule calls DevOps Agent webhook → investigation starts
3. Agent investigates (CloudWatch, logs, topology, etc.)
4. Agent writes findings, root cause, and mitigation plan back into the ServiceNow ticket

### Multicloud / Hybrid / On-Prem

The agent can't directly query Azure or on-prem resources, but gets visibility through:

- **Observability tools** — if resources report to Datadog/Dynatrace/Splunk, the agent sees that telemetry
- **MCP Servers** — custom bridges to Azure APIs or on-prem monitoring
- **Skills** — document the non-AWS parts of the architecture so the agent has context

### IAM Roles

| Role | Who Uses It | What It Does |
|------|------------|-------------|
| Agent Space Role | The agent | Access AWS resources for discovery and investigations |
| Web App Role | Your team | Access the DevOps Agent web app UI |

Web app access: **Admin access** (quick, 10-min session from console) or **IAM Identity Center** (SSO with Okta, Entra ID, etc.)

---

## 🧹 Post-Demo Cleanup

1. Go to **CloudFormation** → select **AWS-DevOpsAgent-EC2-Test** → **Delete**


2. This removes all test resources (EC2, security group, IAM role, alarm)

> 💡 The instance also auto-shuts down after 2 hours if you forget.
