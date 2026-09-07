# VirtualBox Networking — Exact Build Sheet

## Create the four Internal Networks

VirtualBox Manager → Tools → Network Manager is not required for Internal Networks. The names are created by assigning them to adapters.

Use exactly:

```text
VAPT-ATTACKER
VAPT-DMZ
VAPT-PIVOT1
VAPT-TARGET
```

Do not enable VirtualBox DHCP on these networks.

## Adapter matrix

| VM | Adapter 1 | Adapter 2 | Adapter 3 |
|---|---|---|---|
| pfSense | NAT/WAN | VAPT-ATTACKER | VAPT-DMZ |
| Kali | VAPT-ATTACKER | — | — |
| Windows | VAPT-DMZ | — | — |
| Ubuntu | VAPT-DMZ | VAPT-PIVOT1 | — |
| Metasploitable2 | VAPT-PIVOT1 | VAPT-TARGET | — |
| BWA | VAPT-TARGET | — | — |
| IoTGoat | VAPT-TARGET | — | — |

## Why this matters

The two machines that create the pivot boundaries have two NICs:

```text
Ubuntu:
DMZ <-> PIVOT1

Metasploitable:
PIVOT1 <-> TARGET
```

pfSense is deliberately **not** connected to PIVOT1 or TARGET.

## MAC addresses

Leave VirtualBox's generated MAC addresses unique. Never clone a VM with a duplicate MAC and leave both machines active.

## Promiscuous mode

Keep the default restrictive setting. Do not enable promiscuous mode unless a specific lab exercise requires it.

## Host isolation

Do not use Bridged Adapter for vulnerable targets.

Use:

- NAT only on pfSense WAN;
- Internal Network everywhere else.

## Final physical/virtual isolation rule

The host operating system should never need an adapter directly connected to `VAPT-TARGET`.
