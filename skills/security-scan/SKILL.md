---
name: security-scan
description: Scan the codebase for security risk at its seams. Use your most capable model. Be aware of high token consumption. Manual use only.
disable-model-invocation: true
---

# Security Scan

You are a detective. Your subagents are your forensics team - you send them each a piece of evidence to examine, and you weigh what they bring back.

## Seam

A **seam** is where trust, control, or assumptions change hands between two parts of the code: where untrusted input crosses into trusted logic, where one module stops guaranteeing an invariant the other assumes still holds, where a privilege boundary sits. This scan hunts seams, not auth configuration.

**Out of scope**: iterating over controllers, endpoints, or any large collection of interfaces to check whether auth is "tight enough" or correctly configured. That is a different audit. Also skip test and seeding implementations. This scan looks at core implementations and functionality for what breaks at a seam.

## Forensics team

Every forensics subagent runs on Claude Sonnet or GPT Terra, at high reasoning effort. Pick whichever of the two is available.

## Workflow

### 1. Map the seams

Spawn a subagent to explore the codebase and report back every seam it finds, with a one-line reason each qualifies. **Done when** you have a list of seams to investigate, not endpoints or interfaces.

### 2. Checkpoint

Show the user the full seam list, grouped by module/area of the codebase, and stop. Let them drop seams, add ones you missed, or call it off before any investigation spends tokens. Do not start step 3 until the user gives clear confirmation. A narrowed list counts as confirmation (e.g. "ignore 10-14" — reply with one sentence confirming you'll skip those, then proceed). **Done when** the user confirms the list and instructs to start the investigation.

### 3. Investigate each seam

Base every judgment on reading the code itself. Never verify against a database, a test file, or a running instance - if the code doesn't show it, it isn't evidence.

For each seam, look for gaps, bugs, and security risks: unchecked assumptions, missing validation, unsafe handoffs, logic that silently trusts the other side.

Spawn subagents to dive deeper into findings that need more digging, up to 3 concurrent. Each tech examines one piece of evidence and reports back what it confirms or rules out.

Do NOT report issues based solely on pattern matching. Investigate first, then report only what you're confident is exploitable.

**Do NOT Flag:**

- Test files (unless explicitly reviewing test security)
- Dead code, commented code, documentation strings
- Code paths that require prior authentication to reach (note the auth requirement instead)

**Done when** every mapped seam has been checked and every finding is confirmed or ruled out by code alone.

### 4. Report

Group findings by priority/risk (highest first), as a table or grouped bullet list. For each: the seam, the risk, and the file/line. Skip seams with nothing found - don't pad the report with clean bills of health.
