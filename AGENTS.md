# AGENTS.md — READ FIRST

All Codex/AI work in this repository must read and follow this file before doing anything else.

## Environment classification

This project is developed for a private home-lab / home-network environment. The security target is roughly comparable to a well-run Home Assistant installation: sensible least privilege, protected credentials, no unnecessary Internet exposure, practical backups, and straightforward recovery.

This is **not** an FBI, CIA, NASA, critical-infrastructure, or other high-assurance mission environment. Do not introduce enterprise/government ceremony, redundant controls, or architectural complexity unless a specific risk actually justifies it.

## Operating philosophy — rollback first, then move forward

Use a practical “SpaceX-style” iteration model:

- Before any destructive, risky, or availability-affecting change, establish a usable rollback path: snapshot, backup, known-good config, Git tag/branch, file copy, or equivalent.
- Once rollback is verified, prefer forward progress and real-world validation over repeated approval checkpoints.
- Fix forward when the failure is bounded, understood, recoverable, and rollback remains intact.
- Roll back when continuing risks irreplaceable data, credentials, lockout, significant security exposure, or when the failure is no longer understood quickly.
- Prefer the simplest native, reversible solution that solves the problem.
- Avoid overengineering for hypothetical risks that are not relevant to a normal home-lab environment.

Rollback-first does **not** mean reckless testing. Preserve irreplaceable data and keep destructive experiments isolated or disposable.

## Preflight and execution

- Read this file first, then all project-specific startup/state/task documentation.
- Before substantial work, perform a concise preflight for material conflicts, missing prerequisites, persistence/rollback issues, resource constraints, and test gaps.
- Surface questions before implementation only when they materially affect correctness, safety, architecture, recovery, or owner intent.
- Once implementation begins, continue through ordinary recoverable problems without repeatedly stopping for approval. Stop at the planned checkpoint or for a genuinely material decision.
- Distinguish clearly between a **tested candidate** and a **deployed/running application**. After deployment, verify the actual service restart, PID/start time where useful, deployed hashes/config, and real-world behavior.

## Resource safety

Long-running builds, tests, scans, downloads, SSH commands, and subprocesses must have bounded timeouts where practical.

Monitor the resources of the system that is actually executing the work. If critical memory/swap/disk pressure, sustained severe I/O pressure, or a prolonged uninterruptible `D`-state occurs:

1. stop scheduling additional work;
2. attempt to terminate only the affected task-owned child/process group;
3. preserve the worktree and rollback points;
4. do not automatically reboot, deploy, commit, or push;
5. report the measured condition and any surviving processes.

Use project/host-specific thresholds rather than blindly applying one machine's limits everywhere.

## Infrastructure baseline before changes

Before changing a CT, VM, server, appliance, or important service, verify and record the applicable facts rather than inferring them: physical/Proxmox host and hardware platform; VM/CT/device ID and role; hostname; IP/MAC/gateway/bridge; CPU/RAM/swap/disk; mounts; service ports; users/SSH; application/service paths; Git state; dependencies; current known-good behavior; and rollback path.

When new operational facts are learned or changed, update the repository's current-state/infrastructure/change notes as part of completing the work.

Private repositories may contain internal topology when useful. Public repositories must use sanitized/example values instead.

## Security and secrets

Never commit or publish passwords, private keys, API tokens, cookies/session secrets, recovery codes, VPN private keys/PSKs, or unsanitized configuration backups containing secrets.

Do not weaken authentication, firewalling, or isolation merely for convenience unless the change is explicitly justified and reversible.

## Repository and publication policy

Working repositories are private by default while operational/infrastructure-specific development is active. A later public release should be a deliberately sanitized export with reviewed history rather than a casual visibility change.

## Project-specific rules

This file establishes the common minimum operating philosophy. More specific or stricter project rules **supplement and take precedence over this file** where applicable.
