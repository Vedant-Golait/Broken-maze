# OWASP Broken Web Applications (BWA)

## Goal

Place OWASP BWA on the isolated target network:

`10.10.40.0/24`

It must be reachable from the Metasploitable side of the lab, not directly routed from pfSense to Kali.

## Download

OWASP BWA 1.2 files:

https://sourceforge.net/projects/owaspbwa/files/1.2/

The 1.2 directory contains the `OWASP_Broken_Web_Apps_VM_1.2.ova` and compressed archives.

## VirtualBox import

1. Download the `.ova` or `.7z`.
2. If using `.7z`, extract with 7-Zip.
3. Import the `.ova` through VirtualBox's Import Appliance function.
4. Set the VM name to `VAPT-OWASP-BWA`.
5. Set Adapter 1 to Internal Network `VAPT-TARGET`.
6. Disable any NAT/Bridged adapters.

## Addressing

Use:

```text
IP:       10.10.40.20/24
Gateway:  10.10.40.10
```

If the BWA image requires DHCP initially, temporarily provide DHCP on the target network from a controlled lab host, identify the address, then convert it to the static scheme.

## Discovery

From Metasploitable:

```bash
ping -c 3 10.10.40.20
```

From a permitted pivoted session, enumerate the web services.

## Safety

BWA is old and deliberately vulnerable. Keep it on `VAPT-TARGET`. Do not bridge it to your physical LAN and do not port-forward it from WAN.

## Validation

Expected:

- Metasploitable → BWA: yes
- IoTGoat → BWA: local target network visibility
- Kali → BWA without a pivot: no direct route

Snapshot:

`07-bwa-ready`
