# pfSense — Router and Segmentation Firewall

## Goal

Create the only router between the attacker LAN and DMZ. pfSense must **not** provide a routed shortcut into the two deeper lab networks.

## VirtualBox

Create VM: `VAPT-pfSense`

Recommended:

- CPU: 2
- RAM: 2 GB
- Disk: 16 GB
- Adapter 1: NAT — WAN
- Adapter 2: Internal Network `VAPT-ATTACKER` — LAN
- Adapter 3: Internal Network `VAPT-DMZ` — DMZ

Do not attach pfSense to `VAPT-PIVOT1` or `VAPT-TARGET`.

## Download

Official pfSense virtualization documentation:

https://docs.netgate.com/pfsense/en/latest/virtualization/index.html

Install from the official pfSense CE installation media.

## Installation

1. Boot the VM from the pfSense installer ISO.
2. Complete the default installation.
3. Reboot and detach the ISO.
4. At the console, identify interfaces.
5. Assign:
   - WAN = Adapter 1
   - LAN = Adapter 2
   - OPT1/DMZ = Adapter 3
6. Set LAN IPv4 to `10.10.10.1/24`.
7. Set OPT1/DMZ IPv4 to `10.10.20.1/24`.
8. Do not enable DHCP on LAN initially; static addressing keeps the lab deterministic.
9. Enable DHCP on DMZ only if desired; this guide uses static addresses for the core hosts.

## Firewall policy

### LAN

Allow:

- LAN → Internet: required outbound traffic.
- LAN → DMZ: only services needed for the exercise.

Block:

- LAN → `10.10.30.0/24`
- LAN → `10.10.40.0/24`

These routes do not exist on pfSense, so direct traffic should fail.

### DMZ

Allow:

- Windows/Ubuntu → Internet for updates if required.
- DMZ → pfSense DNS/NTP as required.

Do not create routes from pfSense to `10.10.30.0/24` or `10.10.40.0/24`.

## NAT

Keep automatic outbound NAT for:

- `10.10.10.0/24`
- `10.10.20.0/24`

Do not create WAN port forwards.

## Verification

From Kali:

```bash
ping -c 3 10.10.10.1
ping -c 3 10.10.20.10
ping -c 3 10.10.20.20
```

These should work where host firewalls permit.

Then:

```bash
ip route
```

There should be no legitimate routed path through pfSense to `10.10.30.0/24` or `10.10.40.0/24`.

## Safety

Never bridge the WAN adapter directly to an untrusted physical network while vulnerable VMs are running.
