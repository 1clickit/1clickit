# Home Infrastructure Inventory Project

**Status:** Approved/planned — important future project. Do not treat this file as authorization for broad network scanning or infrastructure changes by itself.

Read `README-FIRST.md` first before substantive work. This project should eventually become the owner's local source of truth for home-network hardware, IP/MAC assignments, virtual machines/containers, services, storage relationships, network segments, dependencies, and change history.

## Why this project exists

The home lab has grown beyond what should be kept in human memory or scattered across individual application repositories. Recent work exposed conflicting historical notes about Proxmox host identity and showed that a capable agent can often verify runtime truth directly instead of relying on remembered IP/host mappings.

The goal is a simple local system that answers:

> What is actually on this network, what is it supposed to be, when was it last verified, and what changed?

This should reduce owner memory burden, prevent agents from acting on stale project notes, and provide a durable basis for future network/security work.

## Core principle: configured, observed, verified

Never silently replace one kind of truth with another.

For important facts, preserve three states where useful:

- **Configured** — what the owner/system intends the value to be.
- **Observed** — what a tool, host, switch, ARP/neighbor table, DHCP lease, Proxmox API/CLI, etc. reports now.
- **Verified** — the value accepted after correlating sufficiently independent evidence.

If they disagree, record a discrepancy rather than guessing.

Example:

```yaml
management_ipv4:
  configured: 192.168.3.10
  observed: 192.168.3.10
  verified: 192.168.3.10
  verified_at: 2026-09-08
  evidence:
    - host IPv4 configuration
    - ARP/neighbor MAC correlation
    - Proxmox guest inventory
    - DMI/SMBIOS chassis identity
```

## Important security rule

This inventory may contain private operational information such as:

- internal IP addresses;
- MAC addresses;
- hostnames;
- VM/CT IDs;
- hardware details;
- ports/services;
- storage/mount relationships;
- topology;
- VLAN/subnet assignments;
- rollback references.

It must **not** contain:

- passwords;
- private SSH keys;
- API tokens;
- cookies/session material;
- VPN private keys or PSKs;
- recovery codes;
- unsanitized configuration backups containing secrets.

The future authoritative inventory should preferably be a **local-only Git repository** on a protected management system, with protected backup/mirroring to local storage such as the NAS. It should not depend on a public Git hosting service.

The document in this `1clickit` master repository is the project specification and durable reminder. The future live household inventory should be separate and local.

## Proposed local repository

Suggested structure:

```text
home-infra/
├── README.md
├── CURRENT-STATE.md
├── AGENTS.md
│
├── devices/
│   ├── physical-hosts/
│   ├── switches/
│   ├── access-points/
│   ├── firewalls/
│   ├── nas/
│   └── clients/
│
├── guests/
│   ├── vms/
│   └── containers/
│
├── networks/
│   ├── subnets.yaml
│   ├── vlans.yaml
│   ├── static-addresses.yaml
│   └── dhcp-observations.yaml
│
├── services/
│   ├── home-assistant.yaml
│   ├── downloader.yaml
│   ├── jellyfin.yaml
│   ├── solar-digital-twin.yaml
│   └── ...
│
├── storage/
│   ├── nas-shares.yaml
│   ├── mounts.yaml
│   └── dependencies.yaml
│
├── observations/
│   └── YYYY-MM-DD/
│
└── generated/
    ├── DEVICE-LIST.md
    ├── IP-ADDRESS-MAP.md
    ├── SERVICE-MAP.md
    └── NETWORK-MAP.md
```

Git history becomes the change history. Do not build a database service until the simple repository proves insufficient.

## Proposed record model

A physical-device record may contain:

```yaml
asset_id: mac-mini-proxmox
status: active
role:
  - proxmox-hypervisor

hardware:
  vendor: Apple
  model: Mac mini
  serial: null   # avoid recording if not operationally useful

management:
  hostname: proxmox
  ipv4:
    configured: null
    observed: null
    verified: null
  interfaces:
    - name: null
      mac: null
      mac_vendor: null
      switch_port: null

proxmox:
  node_name: proxmox
  guests: []

verification:
  last_verified_at: null
  methods: []
  notes: null
```

Guest records should reference their physical hypervisor by durable inventory ID rather than only by IP address.

## Website direction

A later phase may provide a simple local web interface generated from or writing back to this inventory.

Desired capabilities:

- dashboard of physical hosts, VMs/containers, switches/APs and important clients;
- searchable IP and MAC map;
- hardware/vendor/model display;
- current host → guest relationships;
- services and listening ports;
- storage/mount dependencies;
- VLAN/subnet membership;
- configured / observed / verified differences;
- last verification time;
- change history from Git;
- update/edit workflow with validation;
- obvious stale-data/discrepancy indicators;
- optional generated topology view.

Keep the initial web application simple and LAN-only. Git/YAML remains the authoritative data source unless a later measured need justifies a database.

## Agent behavior

A future Susan inventory mission may be given broad **read-only discovery authority** inside an owner-approved network scope and may correlate facts from multiple sources.

Discovery sources may include, when explicitly authorized and available:

- Proxmox CLI/API/inventory;
- host `ip addr`, `ip link`, routes and neighbor tables;
- ARP/ND tables;
- DHCP leases;
- OPNsense interface/DHCP/firewall information;
- managed-switch/AP client and MAC tables;
- NAS interface/service information;
- DNS/hostnames;
- existing project documentation;
- bounded service discovery where specifically authorized.

Do not infer administrative authority from discovery authority. The inventory agent should not change firewall, switch, AP, host or service configuration merely to make observations match documentation.

When an observation disagrees with documentation:

1. preserve the observed evidence;
2. seek an independent confirming source where practical;
3. mark the record `disputed` or `needs_verification` if still unresolved;
4. never silently rewrite history to make it appear there was no discrepancy.

## Immediate Proxmox identity verification task — 2026-09-08

Recent screenshots establish the following **expected guest placement**:

```text
Node `proxmox` — owner identifies as Mac mini
├── VM 100  haos18
└── VM 101  media-prototype

Node `pve` — owner identifies as Datto
├── CT 100  codex-workbench
├── CT 102  debian
└── CT 110  video-downloader
```

Expected management mapping from current owner correction:

```text
192.168.3.10   -> node `proxmox` / Mac mini
192.168.3.254  -> node `pve` / Datto
```

However, an earlier observation reportedly associated `192.168.3.10` with vendor text `Datto Inc.`. That observation must be preserved and explained rather than ignored. A MAC OUI/vendor result may identify the network-interface manufacturer and is not by itself proof of chassis identity.

Before publishing current project documentation, Susan should verify both Proxmox hosts from runtime evidence.

### Commands on each Proxmox host

Run read-only:

```bash
hostname
ip -4 -br addr
ip -br link
cat /sys/class/dmi/id/sys_vendor 2>/dev/null
cat /sys/class/dmi/id/product_name 2>/dev/null
qm list
pct list
```

Also useful if more hardware correlation is needed:

```bash
ip route
lscpu | sed -n '1,20p'
lsblk -o NAME,MODEL,SERIAL,SIZE,TYPE,MOUNTPOINTS
```

Do not publish disk serial numbers into general project documentation unless there is a concrete need; they may be retained only in the protected local inventory if useful.

### Correlate from another LAN system

macOS/BSD style:

```bash
arp -an | egrep '192\.168\.3\.(10|254)'
```

Linux style:

```bash
ip neigh show 192.168.3.10
ip neigh show 192.168.3.254
```

Then correlate:

```text
management IP
    -> observed MAC
    -> host interface MAC
    -> hostname
    -> DMI/SMBIOS vendor/product
    -> actual qm/pct guest inventory
```

The final record should explicitly explain any NIC-vendor/OUI result that differs from the chassis vendor.

## First important use case

This project should prevent exactly the kind of confusion that occurred during the 2026-09-08 media-platform prototype, where historical documentation, owner memory and a vendor observation disagreed about which Proxmox host owned which management address.

The desired future answer should be immediately available and evidence-backed, for example:

```text
192.168.3.10
  node: proxmox
  physical asset: mac-mini-proxmox
  chassis vendor/model: verified from DMI
  interface MAC: verified
  MAC/OUI vendor: observed separately
  guests: VM100 haos18, VM101 media-prototype
  last verified: ...

192.168.3.254
  node: pve
  physical asset: datto-proxmox
  chassis vendor/model: verified from DMI
  interface MAC: verified
  MAC/OUI vendor: observed separately
  guests: CT100 codex-workbench, CT102 debian, CT110 video-downloader
  last verified: ...
```

Do not fill these verification fields from memory alone; the example shows the intended presentation.

## Later integration with project repositories

Individual project repositories should not each maintain their own full household inventory.

Instead:

- project docs retain only project-relevant dependencies and operational references;
- the local infrastructure inventory is authoritative for device/network identity;
- project docs may record the inventory asset IDs they depend on;
- agents update the local inventory whenever an authorized change establishes a new durable infrastructure fact;
- project histories may retain old values as historical evidence without pretending they remain current.

Solar Digital Twin should eventually receive a whole-project documentation/runtime audit and be reconciled against this inventory as a separate future mission.

## Future AI-lab relationship

When the smaller AI laboratory is created, the infrastructure inventory should remain outside ordinary lab rewrite authority if practical.

A future safer model is:

```text
Protected authoritative inventory
          |
          | read current truth
          v
       AI Lab
          |
          | append/propose observations
          v
Protected review/history
```

The exact mechanism can remain simple. The important property is that a compromised autonomous lab administrator should not be able to silently erase or falsify the authoritative household inventory and its history.

## Project acceptance criteria

The first useful version is complete when:

1. every important physical network device has a durable asset record;
2. all Proxmox hosts and guests are mapped correctly;
3. known IP/MAC assignments are searchable;
4. important services and storage dependencies are linked to their hosts;
5. configured, observed and verified values are distinct;
6. stale/disputed records are obvious;
7. secrets are absent;
8. Git history preserves every accepted change;
9. a simple local website can render the inventory without becoming the sole source of truth;
10. project-specific repos can point to inventory asset IDs rather than maintaining contradictory host maps.

## Current next action

For now, do only the two-Proxmox-host identity verification described above and feed the verified facts into the Advanced Downloader documentation reconciliation.

Do not begin the broader network inventory or website implementation until the owner explicitly resumes this project.
