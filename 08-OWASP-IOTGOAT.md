# OWASP IoTGoat

## Goal

Place IoTGoat on `VAPT-TARGET` at `10.10.40.30`.

Official project:

https://github.com/OWASP/IoTGoat

Official releases:

https://github.com/OWASP/IoTGoat/releases

OWASP documents `IoTGoat-x86.vmdk`/VDI-style VirtualBox use and specifies Linux 32-bit settings plus PAE/NX for the historical VirtualBox setup.

## VirtualBox

VM: `VAPT-IoTGoat`

Recommended:

- CPU: 1
- RAM: 512 MB–1 GB
- Adapter 1: Internal Network `VAPT-TARGET`
- No NAT or Bridged adapter.

For the x86 image, follow the OS type documented by OWASP:

```text
Type: Linux
Version: Linux 2.6 / 3.x / 4.x (32-bit)
PAE/NX: enabled
```

## Import VDI/VMDK

1. Download the official release.
2. Extract the archive if necessary.
3. Create a Linux VM.
4. Select the IoTGoat VDI/VMDK as the existing disk.
5. Attach Adapter 1 to `VAPT-TARGET`.

## Address

Target:

```text
10.10.40.30/24
Gateway: 10.10.40.10
```

If the image boots with DHCP first, discover the assigned address and then adapt the image's network configuration for the static target address.

## Validation

From Metasploitable:

```bash
ping -c 3 10.10.40.30
```

From the target network, enumerate the device only as part of the lab exercise.

Do not expose IoTGoat to your physical network.

Snapshot:

`08-iotgoat-ready`
