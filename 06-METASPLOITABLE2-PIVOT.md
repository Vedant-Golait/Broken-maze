# Metasploitable2 — Pivot Host

## Goal

Metasploitable2 is the intentionally vulnerable intermediate host connecting:

`VAPT-PIVOT1` ↔ `VAPT-TARGET`

Official download:

https://sourceforge.net/projects/metasploitable/files/Metasploitable2/

The published package is `metasploitable-linux-2.0.0.zip`; it is intentionally vulnerable and should never be exposed to an untrusted network.

## VirtualBox

VM: `VAPT-Metasploitable2`

- CPU: 1
- RAM: 1 GB
- Disk: use imported VMDK
- Adapter 1: Internal Network `VAPT-PIVOT1`
- Adapter 2: Internal Network `VAPT-TARGET`

No NAT/WAN adapter.

## Import

1. Download the archive.
2. Extract it.
3. In VirtualBox create a Linux VM.
4. Select the extracted VMDK as the existing disk.
5. Add the second adapter.
6. Assign Adapter 1 to `VAPT-PIVOT1`.
7. Assign Adapter 2 to `VAPT-TARGET`.

## Addresses

Pivot1:

```text
10.10.30.10/24
```

Target:

```text
10.10.40.10/24
```

Do not configure a default gateway toward the Internet.

## Identify interfaces

```bash
ifconfig -a
```

Configure the two interfaces according to the interface names in the image.

## Enable routing

```bash
sudo sysctl -w net.ipv4.ip_forward=1
```

If needed, persist:

```bash
echo 'net.ipv4.ip_forward=1' | sudo tee /etc/sysctl.d/99-vapt-router.conf
sudo sysctl --system
```

## Validation

From Ubuntu:

```bash
ping -c 3 10.10.30.10
```

From Metasploitable:

```bash
ifconfig
route -n
```

Once BWA and IoTGoat are online:

```bash
ping -c 3 10.10.40.20
ping -c 3 10.10.40.30
```

Kali must not have direct Layer-3 connectivity to `10.10.40.0/24` through pfSense.

Snapshot:

`06-metasploitable-ready`
