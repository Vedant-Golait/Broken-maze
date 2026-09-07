# Broken Maze — Master Architecture

> **Scope:** isolated VirtualBox training environment. Use only against these intentionally vulnerable lab systems.

## 1. Objective

Build a segmented VAPT lab in which the intended movement path is:

**Kali/Parrot → Windows Server → Ubuntu Server → Metasploitable2 → OWASP BWA / OWASP IoTGoat**

The design deliberately prevents Kali from having direct routed access to the deeper networks. Windows is the first DMZ foothold, Ubuntu is the next controlled movement point, Metasploitable2 is the second pivot, and BWA/IoTGoat are the final target segment.

## 2. Logical topology

```text
                         Internet
                            |
                     VirtualBox NAT
                            |
                      pfSense WAN
                            |
                 +----------+-----------+
                 |                      |
          ATTACKER LAN                 DMZ
          10.10.10.0/24           10.10.20.0/24
                 |                      |
        Kali / Parrot             +----+----+
        10.10.10.10               |         |
                                  |         |
                           Windows Server  Ubuntu Server
                           10.10.20.10    10.10.20.20
                                              |
                                              | NIC2
                                              v
                                       PIVOT NET 1
                                       10.10.30.0/24
                                              |
                                       Metasploitable2
                                       10.10.30.10
                                              |
                                              | NIC2
                                              v
                                       TARGET NET
                                       10.10.40.0/24
                                         |        |
                                         |        |
                                      BWA      IoTGoat
                                   .40.20      .40.30
```

### Important routing property

- pfSense routes **10.10.10.0/24 ↔ 10.10.20.0/24**.
- pfSense does **not** route 10.10.10.0/24 directly to 10.10.30.0/24 or 10.10.40.0/24.
- Ubuntu routes between its DMZ NIC and Pivot Net 1.
- Metasploitable2 routes between Pivot Net 1 and Target Net.
- Therefore the deeper networks are reachable only after gaining the relevant intermediate foothold and configuring a lab pivot/tunnel.

## 3. VirtualBox networks

Create these **Internal Networks** exactly:

| VirtualBox network | Subnet | Purpose |
|---|---|---|
| `VAPT-ATTACKER` | 10.10.10.0/24 | Kali/Parrot + pfSense LAN |
| `VAPT-DMZ` | 10.10.20.0/24 | Windows + Ubuntu DMZ side + pfSense DMZ |
| `VAPT-PIVOT1` | 10.10.30.0/24 | Ubuntu ↔ Metasploitable2 |
| `VAPT-TARGET` | 10.10.40.0/24 | Metasploitable2 ↔ BWA ↔ IoTGoat |

Do **not** enable VirtualBox DHCP on these Internal Networks. pfSense or the individual machines will provide addressing as documented.

## 4. VM inventory

| VM | NIC | Network | Address |
|---|---|---|---|
| pfSense | WAN | VirtualBox NAT | DHCP |
| pfSense | LAN | VAPT-ATTACKER | 10.10.10.1 |
| pfSense | DMZ | VAPT-DMZ | 10.10.20.1 |
| Kali/Parrot | NIC1 | VAPT-ATTACKER | 10.10.10.10 |
| Windows Server | NIC1 | VAPT-DMZ | 10.10.20.10 |
| Ubuntu Server | NIC1 | VAPT-DMZ | 10.10.20.20 |
| Ubuntu Server | NIC2 | VAPT-PIVOT1 | 10.10.30.20 |
| Metasploitable2 | NIC1 | VAPT-PIVOT1 | 10.10.30.10 |
| Metasploitable2 | NIC2 | VAPT-TARGET | 10.10.40.10 |
| OWASP BWA | NIC1 | VAPT-TARGET | 10.10.40.20 |
| OWASP IoTGoat | NIC1 | VAPT-TARGET | 10.10.40.30 |

## 5. Where DVWA belongs

DVWA is an application, not a separate VM in this design. Install it on Ubuntu Server.

Recommended URL:

`http://10.10.20.20/dvwa/`

The Ubuntu firewall should allow the DVWA HTTP service from Windows and from the attacker only as permitted by the lab exercise. The intended first foothold remains Windows.

## 6. Security boundaries

### Boundary A — Attacker LAN → DMZ

Kali can:

- ping/scan the DMZ as configured;
- reach the specifically exposed training services;
- attack Windows as the first target.

Kali should **not** have a direct route to Pivot Net 1 or Target Net.

### Boundary B — Windows → Ubuntu

Windows is intended to be the first compromised host.

Ubuntu's exposed services should be restricted so that the attacker cannot simply treat Ubuntu as an unrestricted first-hop target. The recommended implementation is host firewall policy on Ubuntu: allow the required service from Windows and administrative access from the lab management source, while restricting the same service from Kali.

### Boundary C — Ubuntu → Metasploitable2

Ubuntu has a second NIC on `10.10.30.0/24`. Metasploitable2 is reachable on this isolated segment.

### Boundary D — Metasploitable2 → Targets

Metasploitable2 has two NICs. BWA and IoTGoat exist only on `10.10.40.0/24`.

## 7. Internet policy

Only pfSense WAN connects to VirtualBox NAT.

Outbound Internet is allowed primarily for:

- OS/package updates;
- downloading lab tooling;
- time synchronization;
- documentation access.

No port-forwarding from the WAN to vulnerable VMs.

For especially risky exercises, disconnect the WAN adapter after all required updates.

## 8. Snapshots

Take snapshots at these milestones:

1. `00-clean`
2. `01-pfsense-configured`
3. `02-kali-ready`
4. `03-windows-ready`
5. `04-ubuntu-ready`
6. `05-metasploitable-ready`
7. `06-bwa-ready`
8. `07-iotgoat-ready`
9. `08-full-lab-validated`

## 9. Validation philosophy

At every boundary test both:

- **positive reachability:** traffic that should work;
- **negative reachability:** traffic that must fail.

Do not proceed until the negative tests pass. A flat network defeats the purpose of the lab.

## 10. Downloads

Use official/vendor sources where available.

- pfSense documentation: https://docs.netgate.com/pfsense/en/latest/virtualization/index.html
- Kali downloads: https://www.kali.org/get-kali/
- Kali VirtualBox documentation: https://www.kali.org/docs/virtualization/import-premade-virtualbox/
- Metasploitable2 SourceForge: https://sourceforge.net/projects/metasploitable/files/Metasploitable2/
- OWASP IoTGoat: https://github.com/OWASP/IoTGoat/releases
- OWASP IoTGoat VirtualBox guidance: https://github.com/OWASP/IoTGoat/wiki/Getting-started
- OWASP BWA SourceForge: https://sourceforge.net/projects/owaspbwa/files/1.2/
- DVWA: https://github.com/digininja/DVWA

**Source notes:** pfSense supports VirtualBox for testing; Kali provides pre-built VirtualBox images; Metasploitable2 is intentionally vulnerable and warns against exposure to untrusted networks; IoTGoat documents VMDK/VDI use with VirtualBox; BWA 1.2 is an old vulnerable VM and should remain isolated. 

## 11. Build order

1. Create VirtualBox Internal Networks.
2. Create and configure pfSense.
3. Configure Kali/Parrot.
4. Configure Windows Server.
5. Configure Ubuntu with two NICs.
6. Install/configure DVWA on Ubuntu.
7. Configure Metasploitable2 with two NICs.
8. Import/configure BWA.
9. Import/configure IoTGoat.
10. Run the complete validation checklist.
11. Only then begin VAPT/pivot exercises.
