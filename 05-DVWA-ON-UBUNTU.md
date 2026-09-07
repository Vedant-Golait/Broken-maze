# DVWA on Ubuntu Server

## Goal

Install Damn Vulnerable Web Application on the Ubuntu DMZ host.

DVWA is intentionally vulnerable and must remain inside the lab.

Official project:

https://github.com/digininja/DVWA

## Important architecture note

DVWA runs on Ubuntu at:

```text
http://10.10.20.20/dvwa/
```

It does not require a separate VM.

## Install prerequisites

On Ubuntu:

```bash
sudo apt update
sudo apt install -y apache2 mariadb-server php php-mysqli php-gd libapache2-mod-php git
```

## Obtain DVWA

```bash
cd /var/www/html
sudo git clone https://github.com/digininja/DVWA.git dvwa
sudo chown -R www-data:www-data /var/www/html/dvwa
```

## Configure PHP

Check:

```bash
php -v
```

Set required PHP options according to the current DVWA documentation.

## Database

Start MariaDB:

```bash
sudo systemctl enable --now mariadb
```

Open MariaDB:

```bash
sudo mysql
```

Create the lab database/user using the database configuration expected by the DVWA version you installed. Do not reuse production credentials.

## Web server

```bash
sudo systemctl enable --now apache2
sudo systemctl restart apache2
```

Browse from a permitted lab client:

```text
http://10.10.20.20/dvwa/
```

Complete the DVWA setup page.

## Network policy

Do not expose TCP/80 or TCP/443 on the WAN.

For the intended exercise, allow Windows to access DVWA and use firewall rules to control direct Kali access.

## Validation

From Windows:

```powershell
Test-NetConnection 10.10.20.20 -Port 80
```

From Ubuntu:

```bash
curl -I http://127.0.0.1/dvwa/
```

From Kali, test only the service visibility expected by your exercise.

Snapshot:

`05-dvwa-ready`
