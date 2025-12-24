Status:  Accepted
Date:  2025-12-23
Decision Owner:  Mathew
Context: Home Lab Infrastructure

🧠 Context

The home lab contains multiple high-value systems and datasets, including:
Proxmox VE virtual machines (malware lab, Splunk, Security Onion)
Personal research and configuration data
Irreplaceable documents for spouse’s academic work (Windows tablet)
Experimental environments that are intentionally unstable
Data loss risk is high due to:
Frequent VM destruction/rebuilds
Malware detonation
Configuration experimentation
Hardware churn
A centralized, reliable, versioned backup solution is required with minimal operational overhead and a clear growth path.

🎯 Decision

Implement Proxmox Backup Server (PBS) as a dedicated backup appliance hosted on a standalone Intel NUC with:
NVMe storage for OS and metadata
External HDDs (initially via USB adapters, later via JBOD enclosure)
Separate datastores per physical disk
Indefinite retention for critical datasets
Native agents for Linux and Windows endpoints

🏗️ Architecture

Initial (MVP) Design
1× Intel NUC (barebones)
64 GB DDR4 RAM
1× 2 TB NVMe (OS + metadata)
2× 2 TB 3.5″ HDDs (USB-attached)
PBS installed directly on bare metal
Future Expansion
Replace USB adapters with multi-bay JBOD enclosure
Add additional HDDs incrementally
Optional secondary PBS for replication/offsite protection

🔐 Scope of Backups

Proxmox VE VMs (malware lab, Splunk, infra services)
Linux hosts via PBS client
Windows systems (including Surface Pro tablet) via PBS Windows agent
Folder-level backups only (no full disk imaging on endpoints)

👍 Consequences (Positive)

Fast, deduplicated, versioned backups
Granular restore capability (file, folder, VM, snapshot)
Isolation from production workloads
Scales incrementally without redesign
Supports malware lab workflows safely
Protects academic and personal data indefinitely

⚠️ Consequences (Trade-offs)

No active/active clustering (by design)
USB-attached disks are acceptable short-term but not ideal long-term
Requires manual restore selection during PBS failover scenarios
PBS is not general-purpose storage (NAS functions handled elsewhere)

🧩 Alternatives Considered
Option	Reason Rejected
NAS-only (TrueNAS)	Backup workflows less efficient; higher complexity
Cloud-only backups	Cost, bandwidth, privacy concerns
Backup software inside PVE	Failure domain too large
RAID-only local storage	No versioning or off-host protection

📌 Decision Rationale

PBS was selected because it:
Integrates natively with Proxmox VE
Supports Linux and Windows endpoints
Emphasizes data integrity and immutability
Matches enterprise backup design patterns
Fits home lab constraints (noise, power, cost)

🔄 Review Criteria

This decision should be revisited if:
Backup data exceeds single-node capacity
Offsite replication becomes mandatory
Network speeds increase significantly (10 GbE+)
Lab expands to multi-user or semi-production use