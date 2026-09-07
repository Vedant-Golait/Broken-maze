# Ubuntu Server — DMZ + Pivot Network

## Goal

Ubuntu is the controlled bridge between the DMZ and `VAPT-PIVOT1`.

It has **two NICs**:

- DMZ: `10.10.20.20`
- Pivot1: `10.10.30.20`

## VirtualBox

VM: `VAPT-Ubuntu-Server`

- CPU: 2–4
- RAM: 4 GB
- Disk: 40+ GB
- Adapter 1: Internal Network `VAPT-DMZ`
- Adapter 2: Internal Network `VAPT-PIVOT1`

Do not attach a NAT/WAN adapter.

## Static addresses

DMZ NIC:

```text
10.10.20.20/24
Gateway: 10.10.20.1
DNS: 10.10.20.1
```

Pivot NIC:

```text
10.10.30.20/24
NO default gateway
```

Only one default gateway should exist: the DMZ interface.

## Identify interfaces

```bash
ip link
ip addr
```

Assume:

- `enp0s3` = DMZ
- `enp0s8` = Pivot1

Confirm before editing Netplan.

## Netplan example

Edit the YAML under `/etc/netplan/`.

```yaml
network:
  version: 2
  ethernets:
    enp0s3:
      addresses:
        - 10.10.20.20/24
      routes:
        - to: default
          via: 10.10.20.1
      nameservers:
        addresses:
          - 10.10.20.1
    enp0s8:
      addresses:
        - 10.10.30.20/24
```

Apply:

```bash
sudo netplan try
sudo netplan apply
```

Verify:

```bash
ip addr
ip route
```

## Enable forwarding

Do this only because Ubuntu is deliberately being used as a lab router/pivot host.

```bash
sudo sysctl -w net.ipv4.ip_forward=1
```

Persist it:

```bash
echo 'net.ipv4.ip_forward=1' | sudo tee /etc/sysctl.d/99-vapt-pivot.conf
sudo sysctl --system
```

## Host firewall

Use UFW/nftables to implement the lab policy. The exact application ports will be opened in the DVWA document.

The design objective is:

- Windows can reach required Ubuntu services.
- Kali can perform controlled visibility/discovery.
- Kali is not given unrestricted access to the Ubuntu vulnerable service.
- Ubuntu can reach Metasploitable on `10.10.30.10`.
- Ubuntu is the only DMZ host with a second interface into Pivot1.

## Verification

```bash
ping -c 3 10.10.20.1
ping -c 3 10.10.20.10
ping -c 3 10.10.30.10
```

The final ping works only after Metasploitable is configured.

## Routing check

From Kali, `10.10.30.10` should not be directly reachable.

From Ubuntu:

```bash
ip route
```

Expected connected routes include:

```text
10.10.20.0/24
10.10.30.0/24
default via 10.10.20.1
```

Snapshot:

`04-ubuntu-ready`
