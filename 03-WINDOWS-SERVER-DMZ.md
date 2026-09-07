# Windows Server — DMZ Host

## Goal

Windows Server is the first DMZ target and intended initial foothold.

## VirtualBox

VM: `VAPT-Windows-Server`

- CPU: 2–4
- RAM: 4–8 GB
- Disk: 60+ GB
- Adapter 1: Internal Network `VAPT-DMZ`
- No other network adapter.

## OS

Use the Windows Server ISO/version appropriate for your lab license.

Microsoft evaluation/download portal:

https://www.microsoft.com/evalcenter/

## Static network

Example:

```text
IP:       10.10.20.10
Mask:     255.255.255.0
Gateway:  10.10.20.1
DNS:      10.10.20.1
```

Verify:

```powershell
ipconfig /all
ping 10.10.20.1
```

## Hostname

Set:

```text
WIN-SRV
```

Reboot when requested.

## Lab role

This VM can host:

- Active Directory if desired later;
- SMB/RDP;
- intentionally weak lab services;
- Windows security configuration experiments.

Do not expose it through pfSense WAN.

## Validation

From Kali:

```bash
nmap -sV 10.10.20.10
```

From Windows:

```powershell
Test-NetConnection 10.10.20.20 -Port 80
```

The second test should be used after Ubuntu/DVWA is configured.

Snapshot:

`03-windows-ready`
