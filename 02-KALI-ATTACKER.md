# Kali / Parrot — Attacker VM

## Goal

Provide the offensive testing workstation on `VAPT-ATTACKER`.

## Download

Official Kali:

https://www.kali.org/get-kali/

Official pre-built VirtualBox images are available from Kali. 

## VirtualBox

VM: `VAPT-Kali`

- CPU: 2–4
- RAM: 4–8 GB
- Disk: 40+ GB
- Adapter 1: Internal Network `VAPT-ATTACKER`
- Do not attach a second adapter.

If using Parrot, use the same single-network design.

## Kali network

Set a static address:

```text
IP:       10.10.10.10/24
Gateway:  10.10.10.1
DNS:      10.10.10.1
```

Verify:

```bash
ip addr
ip route
ping -c 3 10.10.10.1
```

## Basic tools

```bash
sudo apt update
sudo apt full-upgrade -y
sudo apt install -y nmap net-tools traceroute curl tcpdump
```

Take snapshot:

`02-kali-ready`

## Baseline discovery

```bash
nmap -sn 10.10.10.0/24
nmap -sn 10.10.20.0/24
```

The DMZ should reveal Windows and Ubuntu where ICMP/discovery is permitted.

Test the deeper ranges:

```bash
nmap -sn 10.10.30.0/24
nmap -sn 10.10.40.0/24
```

These should not be directly reachable from Kali.

## Important

Do not add static routes to the deeper networks at this stage. The point of the lab is to practice gaining a foothold and then moving through controlled intermediate hosts.
