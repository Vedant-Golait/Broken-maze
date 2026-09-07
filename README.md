# Broken Maze

> A segmented, intentionally vulnerable penetration-testing lab for practicing reconnaissance, exploitation, lateral movement, pivoting, web application security, and network segmentation in an isolated VirtualBox environment.

[![Platform](https://img.shields.io/badge/Platform-VirtualBox-blue)](https://www.virtualbox.org/)
[![Focus](https://img.shields.io/badge/Focus-VAPT-red)](#objectives)
[![Status](https://img.shields.io/badge/Status-Lab%20Build-orange)](#status)

---

## Overview

**Broken Maze** is a deliberately vulnerable Broken Maze designed around a segmented network architecture.

The environment is intentionally structured so that an attacker cannot simply scan and attack every vulnerable machine from the initial network. Instead, the lab is designed to simulate **network segmentation, DMZ exposure, lateral movement, and multi-stage pivoting**.

The intended progression is:

```text
Attacker
   │
   ▼
pfSense
   │
   ▼
DMZ
   │
   ├── Windows Server
   │
   └── Ubuntu Server
          │
          ▼
   Metasploitable 2
          │
          ▼
   Isolated Vulnerable Network
       ┌──┴───────┐
       ▼          ▼
   OWASP BWA   OWASP IoTGoat
```

The key design principle is:

```text
Initial access
      ↓
Windows Server
      ↓
Horizontal movement
      ↓
Ubuntu Server
      ↓
Pivot
      ↓
Metasploitable 2
      ↓
Pivot / reach isolated network
      ↓
OWASP BWA + OWASP IoTGoat
```

This makes Broken Maze suitable for practicing **realistic attack-path reasoning** instead of treating a vulnerable lab as one flat network.

---

## Objectives

Broken Maze is intended to provide hands-on practice with:

- Network reconnaissance
- Service enumeration
- Vulnerability identification
- Exploitation in a controlled environment
- Web application security
- Windows Server security testing
- Linux security testing
- Horizontal/lateral movement
- Network pivoting
- Route manipulation
- SOCKS/proxy-based access
- Segmented-network enumeration
- Firewall and ACL analysis
- Post-exploitation validation
- Attack-path documentation
- VAPT reporting

---

## Architecture

### High-level topology

```text
                         ┌─────────────────────┐
                         │   Kali / Parrot     │
                         │      Attacker       │
                         └──────────┬──────────┘
                                    │
                                    │ ATTACKER LAN
                                    │
                             ┌──────▼──────┐
                             │   pfSense   │
                             │   Router /  │
                             │   Firewall  │
                             └──────┬──────┘
                                    │
                                    │ DMZ
                                    ▼
                    ┌─────────────────────────────┐
                    │          DMZ NETWORK        │
                    │                             │
                    │  ┌──────────────┐           │
                    │  │Windows Server│           │
                    │  └──────┬───────┘           │
                    │         │                   │
                    │         │ horizontal        │
                    │         │ movement          │
                    │         ▼                   │
                    │  ┌──────────────┐           │
                    │  │Ubuntu Server │           │
                    │  │ + DVWA       │           │
                    │  └──────┬───────┘           │
                    └─────────┼───────────────────┘
                              │
                              │ controlled pivot
                              ▼
                    ┌─────────────────────────────┐
                    │        NETWORK 2            │
                    │                             │
                    │     Metasploitable 2        │
                    │       Pivot Host            │
                    └─────────────┬───────────────┘
                                  │
                                  │ pivot
                                  ▼
                    ┌─────────────────────────────┐
                    │        NETWORK 3            │
                    │                             │
                    │  ┌──────────────┐           │
                    │  │   OWASP BWA  │           │
                    │  └──────────────┘           │
                    │                             │
                    │  ┌──────────────┐           │
                    │  │ OWASP IoTGoat│           │
                    │  └──────────────┘           │
                    └─────────────────────────────┘
```

### Security boundaries

| Segment | Purpose | Example subnet |
|---|---|---|
| Attacker LAN | Kali/Parrot and management access | `10.10.10.0/24` |
| DMZ | Windows and Ubuntu targets | `10.10.20.0/24` |
| Pivot Network | Metasploitable 2 | `10.10.30.0/24` |
| Vulnerable Network | BWA and IoTGoat | `10.10.40.0/24` |

> The exact addresses are documented in [`00-LAB-ARCHITECTURE.md`](00-LAB-ARCHITECTURE.md).

---

## Lab Components

| Component | Role | Documentation |
|---|---|---|
| VirtualBox | Hypervisor | [`09-VIRTUALBOX-NETWORKING.md`](09-VIRTUALBOX-NETWORKING.md) |
| pfSense | Router / firewall | [`01-PFSENSE.md`](01-PFSENSE.md) |
| Kali Linux / Parrot OS | Attacker | [`02-KALI-ATTACKER.md`](02-KALI-ATTACKER.md) |
| Windows Server | DMZ target / lateral movement starting point | [`03-WINDOWS-SERVER-DMZ.md`](03-WINDOWS-SERVER-DMZ.md) |
| Ubuntu Server | DMZ target / pivot bridge / DVWA host | [`04-UBUNTU-SERVER-DMZ-PIVOT.md`](04-UBUNTU-SERVER-DMZ-PIVOT.md) |
| DVWA | Web application target | [`05-DVWA-ON-UBUNTU.md`](05-DVWA-ON-UBUNTU.md) |
| Metasploitable 2 | Pivot host / intentionally vulnerable Linux target | [`06-METASPLOITABLE2-PIVOT.md`](06-METASPLOITABLE2-PIVOT.md) |
| OWASP BWA | Vulnerable web application VM | [`07-OWASP-BWA.md`](07-OWASP-BWA.md) |
| OWASP IoTGoat | Vulnerable IoT target | [`08-OWASP-IOTGOAT.md`](08-OWASP-IOTGOAT.md) |

---

## Documentation

### Start here

1. [`00-LAB-ARCHITECTURE.md`](00-LAB-ARCHITECTURE.md) — complete architecture, addressing plan, traffic flow, and trust boundaries.
2. [`09-VIRTUALBOX-NETWORKING.md`](09-VIRTUALBOX-NETWORKING.md) — VirtualBox networks and adapter mapping.
3. [`01-PFSENSE.md`](01-PFSENSE.md) — pfSense installation and routing/firewall configuration.
4. Build the machines in the order described in the individual machine guides.
5. [`10-PIVOTING-AND-VALIDATION.md`](10-PIVOTING-AND-VALIDATION.md) — connectivity, segmentation, pivot, and final validation.

### Individual machine guides

- [`02-KALI-ATTACKER.md`](02-KALI-ATTACKER.md)
- [`03-WINDOWS-SERVER-DMZ.md`](03-WINDOWS-SERVER-DMZ.md)
- [`04-UBUNTU-SERVER-DMZ-PIVOT.md`](04-UBUNTU-SERVER-DMZ-PIVOT.md)
- [`05-DVWA-ON-UBUNTU.md`](05-DVWA-ON-UBUNTU.md)
- [`06-METASPLOITABLE2-PIVOT.md`](06-METASPLOITABLE2-PIVOT.md)
- [`07-OWASP-BWA.md`](07-OWASP-BWA.md)
- [`08-OWASP-IOTGOAT.md`](08-OWASP-IOTGOAT.md)

---

## Recommended Build Order

Build the lab in this order:

```text
1. VirtualBox
      ↓
2. pfSense
      ↓
3. VirtualBox networks
      ↓
4. Kali / Parrot
      ↓
5. Windows Server
      ↓
6. Ubuntu Server
      ↓
7. DVWA
      ↓
8. Metasploitable 2
      ↓
9. OWASP BWA
      ↓
10. OWASP IoTGoat
      ↓
11. Connectivity validation
      ↓
12. Segmentation validation
      ↓
13. Pivoting exercises
```

Do not begin exploitation until the network-validation document confirms that the intended isolation is working.

---

## Download Sources

Use official project sources where available.

### VirtualBox

[VirtualBox Downloads](https://www.virtualbox.org/wiki/Downloads)

### pfSense

[pfSense CE Download](https://www.pfsense.org/download/)

### Kali Linux

[Kali Linux Downloads](https://www.kali.org/get-kali/)

[Kali Linux VirtualBox Images](https://www.kali.org/get-kali/#kali-virtual-machines)

### Parrot OS

[Parrot OS Downloads](https://parrotsec.org/download/)

### Windows Server

Obtain Windows Server from Microsoft's official evaluation/download channels:

[Microsoft Evaluation Center](https://www.microsoft.com/en-us/evalcenter/)

### Ubuntu Server

[Ubuntu Server Downloads](https://ubuntu.com/download/server)

### Metasploitable 2

[Rapid7 Metasploitable 2 on SourceForge](https://sourceforge.net/projects/metasploitable/)

### OWASP Broken Web Applications

[OWASP BWA Project](https://owasp.org/www-project-broken-web-applications/)

[BWA 1.2 Downloads](https://sourceforge.net/projects/owaspbwa/files/1.2/)

### OWASP IoTGoat

[OWASP IoTGoat GitHub Repository](https://github.com/OWASP/IoTGoat)

[OWASP IoTGoat Releases](https://github.com/OWASP/IoTGoat/releases)

> Always verify downloaded images against the official project source before importing them into VirtualBox.

---

## VirtualBox Network Design

The lab uses multiple isolated VirtualBox networks rather than putting all machines on one bridged or host-only segment.

The intended logical separation is:

```text
ATTACKER
10.10.10.0/24
      │
      │
   pfSense
      │
      ▼
DMZ
10.10.20.0/24
      │
      ├── Windows Server
      │
      └── Ubuntu + DVWA
               │
               ▼
          Pivot Network
          10.10.30.0/24
               │
               └── Metasploitable 2
                        │
                        ▼
                 Vulnerable Network
                   10.10.40.0/24
                    ├── OWASP BWA
                    └── OWASP IoTGoat
```

The detailed adapter-to-network mapping is maintained separately so the README remains a high-level project entry point.

---

## Intended Attack Path

Broken Maze is deliberately designed around constrained access.

### Stage 1 — Initial access

The attacker starts from Kali/Parrot and performs reconnaissance against the DMZ.

```text
Kali / Parrot
      │
      ▼
Windows Server
```

### Stage 2 — Horizontal movement

The attacker uses the Windows foothold to reach the Ubuntu host.

```text
Windows Server
      │
      ▼
Ubuntu Server
```

The goal is to prevent the attacker from treating Ubuntu as another unrestricted initial-access target.

### Stage 3 — Pivot

Ubuntu provides the controlled path toward the Metasploitable segment.

```text
Ubuntu Server
      │
      ▼
Metasploitable 2
```

### Stage 4 — Deeper network access

Metasploitable provides access toward the isolated vulnerable network.

```text
Metasploitable 2
      │
      ▼
10.10.40.0/24
   ┌──┴───────┐
   ▼          ▼
 BWA       IoTGoat
```

This creates a practical environment for studying **multi-stage network pivoting**.

---

## Validation Philosophy

A successful installation is not enough.

Every segment should be tested for both:

### Expected connectivity

Examples:

- Attacker → pfSense
- Attacker → permitted DMZ services
- Windows → Ubuntu
- Ubuntu → Metasploitable
- Metasploitable → BWA
- Metasploitable → IoTGoat

### Expected isolation

Examples:

- Attacker should **not** have unrestricted direct access to Network 3.
- Windows should **not** directly reach Metasploitable.
- DMZ systems should not bypass the intended pivot path.
- BWA and IoTGoat should not be exposed to the host's physical LAN.
- No vulnerable VM should be accidentally bridged directly to the Internet.

The complete validation procedure is in [`10-PIVOTING-AND-VALIDATION.md`](10-PIVOTING-AND-VALIDATION.md).

---

## Safety

**Broken Maze is intentionally vulnerable.**

Run it only in an isolated environment that you control.

### Do not

- Expose vulnerable VMs directly to the public Internet.
- Bridge vulnerable machines directly onto a production/home LAN.
- Port-forward vulnerable services from the Internet.
- Reuse real credentials or secrets inside the lab.
- Store real personal data on vulnerable machines.

### Recommended model

```text
Internet
   │
   ▼
Host machine
   │
   ▼
pfSense WAN
   │
   ├── controlled outbound access
   │
   └── isolated lab networks
```

The vulnerable networks should remain behind pfSense and should not be reachable from the physical LAN unless explicitly required for a controlled administrative purpose.

---

## Repository Structure

```text
Broken-Maze/
│
├── README.md
│
├── 00-LAB-ARCHITECTURE.md
├── 01-PFSENSE.md
├── 02-KALI-ATTACKER.md
├── 03-WINDOWS-SERVER-DMZ.md
├── 04-UBUNTU-SERVER-DMZ-PIVOT.md
├── 05-DVWA-ON-UBUNTU.md
├── 06-METASPLOITABLE2-PIVOT.md
├── 07-OWASP-BWA.md
├── 08-OWASP-IOTGOAT.md
├── 09-VIRTUALBOX-NETWORKING.md
└── 10-PIVOTING-AND-VALIDATION.md
```

---

## Status

**Current status:** Documentation and environment design.

The repository documents the intended architecture and build process. Individual lab components can be brought online incrementally and validated before moving to the next stage.

---

## Learning Outcomes

By completing Broken Maze, you should gain practical experience with:

- Building segmented virtual networks
- Configuring a virtual firewall/router
- Understanding DMZ architecture
- Performing reconnaissance across trust boundaries
- Identifying attack paths
- Exploiting intentionally vulnerable systems
- Moving laterally between hosts
- Establishing and validating pivots
- Accessing otherwise unreachable networks
- Understanding routing versus application-layer access
- Troubleshooting multi-interface virtual machines
- Documenting a VAPT engagement

---

## Disclaimer

This project is intended **solely for authorized security education and controlled laboratory testing**.

The vulnerable systems included in this environment are intentionally insecure. Do not deploy them in environments where unauthorized users or untrusted networks can access them.

You are responsible for ensuring that all testing is performed against systems and networks for which you have explicit authorization.

---

## Credits and Upstream Projects

Broken Maze uses or is inspired by intentionally vulnerable projects maintained by their respective communities:

- [OWASP](https://owasp.org/)
- [OWASP Broken Web Applications](https://owasp.org/www-project-broken-web-applications/)
- [OWASP IoTGoat](https://github.com/OWASP/IoTGoat)
- [Rapid7 Metasploitable](https://sourceforge.net/projects/metasploitable/)
- [Kali Linux](https://www.kali.org/)
- [Parrot OS](https://parrotsec.org/)
- [pfSense](https://www.pfsense.org/)
- [Ubuntu](https://ubuntu.com/)
- [Oracle VirtualBox](https://www.virtualbox.org/)

This repository does not claim ownership of those projects or their distributions.

---

## License

The documentation in this repository can be licensed independently from the third-party VM images and software used by the lab.

Third-party software, VM images, trademarks, and vulnerable applications remain subject to their respective licenses and terms.

If you distribute this repository, do **not** redistribute third-party VM images unless their respective licenses explicitly permit it. Prefer linking users to the official download sources above.
