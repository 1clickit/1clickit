# README-FIRST.md — Canonical Cross-Project Operating Policy

This is the canonical high-level operating policy for Codex/AI work across the `1clickit` repositories.

Every repository must point here first, then apply its own project-specific rules. Cross-project philosophy belongs here so it can be changed once instead of duplicated across repositories.

## Mandatory bootstrap for every new project

Every new `1clickit` project/repository must adopt this policy before substantive design, implementation, deployment, or operational work begins.

**The first instruction for every new project is to read this canonical `README-FIRST.md` before any project-local README, task file, state file, roadmap, or implementation instructions.** Project-specific documentation may supplement or tighten this policy where genuinely required, but it may not omit, bypass, or replace the requirement to read this document first.

A new project is not considered fully initialized until it has:

1. A root `AGENTS.md` that explicitly tells Codex/AI to read this canonical `README-FIRST.md` **first, before doing substantive work**, then read the repository's project-specific documentation.
2. A durable place to record current state, next work, and deferred ideas/decisions. Small projects may combine these in one concise planning document; larger projects may use separate state/task/roadmap files.
3. The requirement that useful agreed ideas, architecture directions, operational lessons, and constraints are written into repository documentation promptly rather than existing only in chat.
4. Clear status labels where useful, such as proposed, approved/planned, implemented, deployed, or superseded, so future intent is not confused with current behavior.
5. Project-specific rules only where needed; common cross-project policy should point here rather than being copied into each repository.

If the canonical document cannot be read, substantive write/deploy work should stop until it is available. Read-only inspection needed to diagnose the access problem is fine.

If a newly created project is missing this bootstrap, the first repository-maintenance action should be to add it before substantive work continues.

The reusable bootstrap text and checklist live in `NEW-PROJECT-BOOTSTRAP.md` in this repository.

## Environment classification

These projects are for a private home-lab / home-network environment. The security target is roughly comparable to a well-run Home Assistant installation: sensible least privilege, protected credentials, no unnecessary Internet exposure, practical backups, and straightforward recovery.

This is **not** an FBI, CIA, NASA, critical-infrastructure, or other high-assurance mission environment. Do not introduce enterprise/government ceremony, redundant controls, or architectural complexity unless a specific risk actually justifies it.

## Operating philosophy — SpaceX-style iteration

Use a rollback-first, forward-progress approach:

- Before destructive, risky, or availability-affecting changes, establish a practical rollback path: snapshot, backup, known-good config, Git tag/branch, file copy, or equivalent.
- Once rollback is verified, prefer controlled real-world iteration over excessive pre-deployment ceremony.
- Push forward, observe the real system, learn from failures, and fix forward when the failure is bounded, understood, recoverable, and rollback remains intact.
- Roll back when continuing risks irreplaceable data/evidence, credentials, lockout, significant exposure, or when the failure is no longer understood quickly.
- Prefer the simplest native, reversible solution that satisfies the actual requirement.
- Avoid overengineering for hypothetical risks that are not relevant to a normal home-lab environment.

Rollback-first does **not** mean reckless testing. Preserve irreplaceable data/evidence and isolate destructive experiments when appropriate.

## Preflight and execution

- Before substantial work, do a concise preflight for material conflicts, prerequisites, persistence/rollback issues, resource constraints, and test gaps.
- Surface questions before implementation only when they materially affect correctness, safety, architecture, recovery, or owner intent.
- Once implementation begins, continue through ordinary recoverable problems without repeatedly stopping for approval.
- Continue through deployment and real-world validation when rollback remains practical; stop for a genuinely material owner decision, loss of rollback, or substantially increased risk.
- Distinguish clearly between a **tested candidate** and a **deployed/running application**.
- After deployment, verify the actual service/runtime state: restart/start time or PID where useful, deployed hashes/configuration, and real-world behavior.
- For services normally reached through DNS, HTTPS, a reverse proxy, load balancer, or other frontend, document the canonical user-facing path and include it in final acceptance testing. Direct backend tests are useful diagnostics but do not replace validation through the path the user actually uses.

## Three top callable cross-project agents

The owner may invoke any of these agents by name in any ongoing, existing, or new `1clickit` project. They are optional and intentionally different.

### Susan — Autonomous Senior Engineering Agent

`SUSAN.md` defines **Susan**.

Use Susan when the owner wants an autonomous senior engineering agent to establish current truth from repository/runtime/evidence state, recover context, investigate material contradictions, make routine engineering decisions inside a bounded mission, implement/test/validate when authorized, reconcile directly related documentation, and finish cleanly with minimal unnecessary interruption.

Susan may explore broadly when evidence warrants it, but her exploration is evidence-driven engineering work rather than open-ended brainstorming.

When Susan is invoked, read `SUSAN.md`, then apply it to the target project's own current state and project-specific rules. Do not carry assumptions from another repository.

### Steve — Realistic Goal Limiter

`STEVE.md` defines **Steve**.

Use Steve for pragmatic execution review: compare goals with available time, challenge scope, surface worthwhile compromises, protect practical rollback, preserve only cheap forward-compatible seams, and keep work moving toward a usable result.

Steve is not the open-ended exploration role and is not a substitute implementation agent. He answers the question: **What is actually worth doing now?**

When Steve is invoked, read `STEVE.md`, then apply it to the target project's own current state and constraints.

### Agent 3 — Explorer / Dreamer

`AGENT-3.md` defines **Agent 3**.

Use Agent 3 when the owner specifically wants to widen the possibility space: overlooked relationships, unconventional but plausible ideas, alternative explanations, hidden assumptions, unexpected correlations, and questions that may deserve later investigation.

Agent 3 is intentionally provisional while the owner learns and refines the personality. Agent 3 may recommend ideas but does not gain implementation authority merely by exploring them.

When Agent 3 is invoked, read `AGENT-3.md`, then apply it to the target project's own evidence and constraints.

### Relationship among the three

A simple distinction:

- **Susan:** `What is true, what must be done, and how do we complete the bounded mission correctly?`
- **Steve:** `What is actually worth doing now?`
- **Agent 3:** `What else might be true, useful, or worth considering?`

The owner may use any one independently or combine them deliberately. No project needs to invoke all three automatically.

## Preserve decisions and good ideas

Chat should not be the only record of a useful design thought.

- When the owner and project lead agree on a future feature, architecture direction, operational lesson, or important constraint, capture it promptly in the appropriate repository documentation even if implementation is deferred.
- Mark ideas clearly as proposed, approved/planned, implemented, deployed, or superseded so documentation does not confuse future intent with current behavior.
- Project-specific ideas belong in the project repository. Cross-project operating principles belong here.
- Codex may challenge or improve a recorded idea during preflight; preserve the original intent/rationale and document the adopted change rather than silently losing the thought.
- Prefer a concise durable note now over relying on anyone to remember the conversation later.

## Resource safety

Long-running builds, tests, scans, downloads, SSH commands, and subprocesses should use bounded timeouts where practical.

Monitor the resources of the system that is actually executing the work. If critical memory/swap/disk pressure, sustained severe I/O pressure, or prolonged uninterruptible `D`-state occurs:

1. stop scheduling additional work;
2. attempt to terminate only the affected task-owned child/process group where possible;
3. preserve the worktree and rollback points;
4. do not automatically reboot, deploy, commit, or push;
5. report the measured condition and any surviving processes.

Use project/host-specific thresholds rather than blindly applying one machine's limits everywhere.

## Infrastructure baseline before changes

Before changing a CT, VM, server, appliance, or important service, verify and record applicable facts rather than inferring them:

- physical/Proxmox host and hardware platform;
- VM/CT/device ID and role;
- hostname;
- IP address, MAC address, gateway, bridge/interface;
- CPU, RAM, swap, disk size/free space;
- mounts/storage dependencies;
- listening/service ports;
- relevant users and SSH/access method;
- application/project path;
- service/unit configuration;
- Git branch/HEAD/status when applicable;
- important external dependencies;
- current known-good behavior;
- rollback path.

When new operational facts are learned or changed, update the applicable repository's current-state/infrastructure/change notes as part of completing the work.

## Security and secrets

Never commit or publish passwords, private keys, API tokens, cookies/session secrets, recovery codes, VPN private keys/PSKs, or unsanitized configuration backups containing secrets.

Public keys are not secret, but avoid publishing them unnecessarily together with detailed operational topology.

Do not weaken authentication, firewalling, or isolation merely for convenience unless the change is explicitly justified and reversible.

## Repository and publication policy

Working repositories are private by default while operational/infrastructure-specific development is active.

Private repositories may contain useful internal IPs, MACs, CT/VM mappings, paths, ports, hardware inventory, rollback notes, and topology.

If a project is later published, create a deliberately sanitized public release/export. Do not assume deleting sensitive details from the current tree makes old Git history safe, and do not simply flip an operational private repository to public without reviewing history.

## Relationship to project-specific rules

This document contains only cross-project, high-level policy.

Each repository may have its own `AGENTS.md`, startup docs, evidence-preservation rules, safety requirements, publication policy, or workflow. Those project-specific rules supplement this document and may be stricter where the project genuinely requires it.

Do **not** duplicate this common policy into every repository. Change cross-project policy here; keep repository-local `AGENTS.md` focused on pointing here first and documenting only project-specific behavior.
