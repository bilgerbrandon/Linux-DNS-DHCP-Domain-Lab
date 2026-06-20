# Linux Active Directory Lab Setup Guide

## Project Overview

This project demonstrates how to build a Linux-based Active Directory environment using Ubuntu Server, Samba Active Directory Domain Services, DNS, Kerberos, and an Ubuntu client.

---

## Lab Architecture

### Domain Controller

* Ubuntu Server
* Samba Active Directory Domain Controller
* DNS Server
* Kerberos Authentication

### Client

* Ubuntu Desktop
* SSSD
* Kerberos Client
* Domain Joined System

---

## Network Information

| Component         | IP Address   |
| ----------------- | ------------ |
| Domain Controller | 192.168.64.4 |
| Client            | DHCP         |
| Domain Name       | lab.local    |
| Realm             | LAB.LOCAL    |

---

## Installation Steps

### Install Ubuntu Server

Install Ubuntu Server and configure networking.

Verify connectivity:

```bash
ping google.com
```

---

### Install Samba

```bash
sudo apt update
sudo apt install samba samba-ad-dc samba-ad-provision krb5-user winbind dnsutils -y
```

---

### Provision Active Directory

```bash
sudo samba-tool domain provision --use-rfc2307 --interactive
```

Example values:

```text
Realm: LAB.LOCAL
Domain: LAB
Server Role: dc
DNS Backend: SAMBA_INTERNAL
DNS Forwarder: 192.168.64.1
```

---

### Configure Samba

Edit:

```bash
sudo nano /etc/samba/smb.conf
```

Add:

```ini
interfaces = lo enp0s1
bind interfaces only = yes
dns forwarder = 192.168.64.1
```

---

### Start Active Directory

```bash
sudo systemctl disable smbd nmbd winbind --now
sudo systemctl enable samba-ad-dc --now
sudo systemctl restart samba-ad-dc
```

Verify:

```bash
sudo systemctl status samba-ad-dc
```

---

### Configure Client DNS

```bash
sudo resolvectl dns enp0s1 192.168.64.4
sudo resolvectl domain enp0s1 lab.local
```

Verify:

```bash
resolvectl status
```

---

### Join Client to Domain

Install required packages:

```bash
sudo apt install realmd sssd sssd-tools adcli krb5-user packagekit -y
```

Authenticate:

```bash
kinit Administrator@LAB.LOCAL
```

Join:

```bash
sudo realm join lab.local -U Administrator
```

Verify:

```bash
realm list
```

---

## Skills Demonstrated

* Linux Administration
* Active Directory
* Samba
* DNS
* Kerberos
* Domain Authentication
* Client Management
* Network Troubleshooting
