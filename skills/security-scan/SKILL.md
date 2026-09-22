---
name: security-scan
description: Scan the codebase for security risk at its seams. Use your most capable model. Be aware of high token consumption. Manual use only.
disable-model-invocation: true
---

# Security Scan

You are a detective. Your subagents are your forensics team - you send them each a piece of evidence to examine, and you weigh what they bring back.

## Seam

A **seam** is where trust, control, or assumptions change hands between two parts of the code: where untrusted input crosses into trusted logic, where one module stops guaranteeing an invariant the other assumes still holds, where a privilege boundary sits. This scan hunts seams, not auth configuration.

Think core, load-bearing seams, not the app's whole surface: the auth-handling middleware itself, file access, client-to-backend authentication, session/token validation, and similar chokepoints every request or file passes through. One bug here is one bug affecting everything downstream of it.

**Out of scope**: iterating over controllers, endpoints, or any large collection of interfaces to check whether auth is "tight enough" or correctly configured. That is a different audit, and it applies just as much at the level of individual functions — do not walk every controller method checking for an `@Authorized`-style annotation, and do not walk every service-layer function checking its access control one by one. Also skip test and seeding implementations. This scan looks at core implementations and functionality for what breaks at a seam.

## Forensics team

Every forensics subagent runs on Claude Sonnet or GPT Terra, at high reasoning effort. Pick whichever of the two is available.

## Workflow

### 1. Map the seams

Spawn a subagent to explore the codebase for seams. For each one it finds, it must also take a first pass at the seam itself: does it show a real vulnerability, gap, or bypass risk?

**High risk** means the seam could plausibly lead to one of these, not just "looks a bit off":

- Privilege escalation (regular user reaching admin/superuser capability)
- Injection (SQL, command, template, or similar)
- Auth bypass (reaching a protected action or resource without the check that should gate it)
- Sensitive data exposure or exfiltration
- Direct/insecure object access (IDOR — reaching another user's data via an ID alone)
- Path or file traversal

Only report a seam back if the first pass found one of these plausible, or if the subagent couldn't dig deep enough to rule it out. Drop everything else silently — vague unease, style nits, or "could be tightened" don't make the list. **Done when** you have a short list of seams that clear this bar, not every seam in the codebase.

### 2. Checkpoint

Show the user the full seam list, grouped by module/area of the codebase, and stop. Let them drop seams, add ones you missed, or call it off before any investigation spends tokens. Do not start step 3 until the user gives clear confirmation. A narrowed list counts as confirmation (e.g. "ignore 10-14" — reply with one sentence confirming you'll skip those, then proceed). **Done when** the user confirms the list and instructs to start the investigation.

### 3. Investigate each seam

Spawn a single forensic team subagent per seam - up to 4 max concurrently.

Base every judgment on reading the code itself. Never verify against a database, a test file, or a running instance - if the code doesn't show it, it isn't evidence.

Look for gaps, bugs, and security risks: unchecked assumptions, missing validation, unsafe handoffs, logic that silently trusts the other side.

Do NOT report issues based solely on pattern matching. Investigate first, then report only what you're confident is exploitable.

**Do NOT Flag:**

- Test files (unless explicitly reviewing test security)
- Dead code, commented code, documentation strings
- Code paths that require prior authentication to reach (note the auth requirement instead)

**Done when** every mapped seam has been checked and every finding is confirmed or ruled out by code alone.

### 4. Report

Group findings by priority/risk (highest first), as a table or grouped bullet list. For each: the seam, the risk, and the file/line. Skip seams with nothing found - don't pad the report with clean bills of health.
