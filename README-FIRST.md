# README-FIRST.md — Canonical Cross-Project Operating Policy

This is the canonical high-level operating policy for Codex/AI work across the `1clickit` repositories.

Every repository should point here first, then apply its own project-specific rules. Cross-project philosophy belongs here so it can be changed once instead of duplicated across repositories.

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
- Stop at planned checkpoints or for genuinely material decisions.
- Distinguish clearly between a **tested candidate** and a **deployed/running application**.
- After deployment, verify the actual service/runtime state: restart/start time or PID where useful, deployed hashes/configuration, and real-world behavior.

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
