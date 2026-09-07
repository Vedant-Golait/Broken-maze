# Pivoting and Full-Lab Validation

## Important distinction

This lab has **two network pivot boundaries**:

1. DMZ → Pivot1 through Ubuntu.
2. Pivot1 → Target through Metasploitable2.

The intended movement chain therefore has three practical compromise/movement stages:

```text
Stage 1: Attacker → Windows
Stage 2: Windows → Ubuntu → Metasploitable2
Stage 3: Metasploitable2 → BWA / IoTGoat
```

This matches the architecture without inventing additional networks.

## Phase 1 — baseline

From Kali:

```bash
ip addr
ip route
```

Expected local gateway:

```text
10.10.10.1
```

Confirm:

```bash
ping -c 3 10.10.10.1
ping -c 3 10.10.20.10
ping -c 3 10.10.20.20
```

## Phase 2 — verify no direct deep access

From Kali:

```bash
ip route get 10.10.30.10
ip route get 10.10.40.20
```

The route should not point to a pfSense path that gives direct access.

Do not add static routes at this stage.

## Phase 3 — Windows foothold

Perform your VAPT exercise against the Windows lab host.

Once a legitimate lab foothold exists, verify the Windows host can communicate with Ubuntu:

```powershell
ping 10.10.20.20
Test-NetConnection 10.10.20.20 -Port 80
```

## Phase 4 — Ubuntu visibility

From Ubuntu:

```bash
ip addr
ip route
ping -c 3 10.10.30.10
```

The important observation is that Ubuntu has connectivity to Metasploitable because it owns a NIC on Pivot1.

## Phase 5 — Metasploitable

On Metasploitable:

```bash
ifconfig
route -n
ping -c 3 10.10.40.20
ping -c 3 10.10.40.30
```

## Phase 6 — target network

BWA:

```text
10.10.40.20
```

IoTGoat:

```text
10.10.40.30
```

Metasploitable:

```text
10.10.40.10
```

They should be mutually reachable as permitted by their own host firewalls.

## Negative tests

From Kali, before a pivot:

```bash
nmap -Pn -p 80,443,22 10.10.40.20
nmap -Pn -p 80,443,22 10.10.40.30
```

The tests should fail to establish direct connectivity.

From Windows, Kali should not be able to use Windows as a routing shortcut to Pivot1 unless the lab exercise explicitly establishes that tunnel.

## Troubleshooting order

Always troubleshoot in this order:

1. VirtualBox adapter attachment.
2. Link/interface state.
3. IP address.
4. Local subnet connectivity.
5. Default gateway.
6. Host firewall.
7. Router/firewall rules.
8. Routing table.
9. Application/service listener.
10. Pivot/tunnel configuration.

## Packet capture

Use pfSense packet capture or `tcpdump` on Ubuntu/Metasploitable to determine exactly where traffic stops.

Ubuntu:

```bash
sudo tcpdump -ni any host 10.10.30.10
```

Metasploitable:

```bash
sudo tcpdump -ni any host 10.10.40.20
```

## Completion criteria

The lab is complete when:

- pfSense WAN reaches the Internet.
- Kali reaches pfSense and intended DMZ services.
- Windows reaches Ubuntu as designed.
- Ubuntu reaches Metasploitable on Pivot1.
- Metasploitable reaches BWA and IoTGoat on Target.
- Kali cannot directly route to Pivot1 or Target.
- No vulnerable target is exposed through WAN.
- Snapshots exist for every stable milestone.
