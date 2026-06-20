# Linux Active Directory Lab - Troubleshooting Guide

## Overview

This document outlines the issues encountered while building a Linux-based Active Directory environment using Ubuntu Server, Samba Active Directory Domain Services (AD DS), DNS, Kerberos, and an Ubuntu client. It also documents the troubleshooting steps used to resolve each issue.

---

# Issue 1: Netplan Configuration Errors

## Symptoms

Running:

```bash
sudo netplan apply
```

produced errors similar to:

```text
Error in network definition: expected either scalar or mapping
```

## Cause

Improper YAML formatting and indentation.

## Resolution

Corrected the Netplan configuration:

```yaml
network:
  version: 2
  ethernets:
    enp0s1:
      addresses:
        - 192.168.1.100/24
      routes:
        - to: default
          via: 192.168.1.1
      nameservers:
        addresses:
          - 8.8.8.8
```

Applied changes:

```bash
sudo netplan apply
```

---

# Issue 2: Unable to Reach Gateway

## Symptoms

The server could ping itself but not:

```bash
ping 192.168.1.1
```

## Cause

Incorrect gateway configuration combined with UTM Shared Networking.

## Resolution

Verified routing table:

```bash
ip route
```

Configured the proper gateway supplied by the UTM virtual network.

---

# Issue 3: BIND Zone File Errors

## Symptoms

BIND reported:

```text
syntax error
zone not loaded due to errors
```

## Cause

Incorrect DNS zone file formatting.

## Resolution

Validated zone file:

```bash
sudo named-checkzone lab.local /etc/bind/db.lab.local
```

Corrected formatting until:

```text
loaded serial X
OK
```

was returned.

---

# Issue 4: Samba Domain Provision Failure

## Symptoms

Domain provisioning failed with errors referencing:

```text
realm not specified
```

or

```text
AD schema file not found
```

## Cause

Required Samba AD provisioning packages were not installed.

## Resolution

Installed missing packages:

```bash
sudo apt install samba-ad-provision
```

Removed previous Samba configuration:

```bash
sudo mv /etc/samba/smb.conf /etc/samba/smb.conf.bak
```

Re-ran provisioning:

```bash
sudo samba-tool domain provision --use-rfc2307 --interactive
```

---

# Issue 5: Samba AD Service Failed to Start

## Symptoms

```bash
sudo systemctl status samba-ad-dc
```

showed:

```text
Failed to bind to 0.0.0.0:53
```

## Cause

BIND9 was already using port 53.

## Resolution

Stopped and disabled BIND:

```bash
sudo systemctl stop bind9
sudo systemctl disable bind9
sudo pkill named
```

Verified port availability:

```bash
sudo ss -tulpn | grep :53
```

Restarted Samba:

```bash
sudo systemctl restart samba-ad-dc
```

---

# Issue 6: Samba DNS Listening Only on IPv6

## Symptoms

DNS queries to:

```text
192.168.64.4
```

failed.

Port 53 was only listening on:

```text
[::]:53
```

## Cause

Samba was not explicitly bound to the network interface.

## Resolution

Modified:

```bash
sudo nano /etc/samba/smb.conf
```

Added:

```ini
interfaces = lo enp0s1
bind interfaces only = yes
dns forwarder = 192.168.64.1
```

Restarted Samba:

```bash
sudo systemctl restart samba-ad-dc
```

Verified:

```bash
sudo ss -tulpn | grep :53
```

showed:

```text
192.168.64.4:53
```

---

# Issue 7: Kerberos and Domain Join Failures

## Symptoms

```bash
realm discover
realm join
kinit
```

failed with:

```text
No such realm found
```

or

```text
Resource temporarily unavailable
```

## Cause

Client system DNS was still pointing to:

```text
127.0.0.53
```

instead of the Domain Controller.

## Resolution

Configured client DNS:

```bash
sudo resolvectl dns enp0s1 192.168.64.4
sudo resolvectl domain enp0s1 lab.local
```

Verified:

```bash
resolvectl status
```

showed:

```text
DNS Servers: 192.168.64.4
DNS Domain: lab.local
```

---

# Issue 8: Kerberos Realm Mismatch

## Symptoms

```bash
kinit Administrator@lab.local
```

returned:

```text
KDC reply did not match expectations
```

## Cause

Kerberos realm names are case-sensitive.

## Resolution

Used:

```bash
kinit Administrator@LAB.LOCAL
```

Result:

```text
Authenticated to Kerberos v5
```

---

# Issue 9: Successful Domain Join

## Verification

Joined the Ubuntu client to the Active Directory domain:

```bash
sudo realm join lab.local -U Administrator
```

Verified:

```bash
realm list
```

Output:

```text
realm-name: LAB.LOCAL
configured: kerberos-member
server-software: active-directory
client-software: sssd
```

---

# Lessons Learned

* DNS is the foundation of Active Directory.
* Kerberos relies heavily on proper DNS records.
* Samba AD DS can fully replace a Windows Domain Controller in a lab environment.
* Port conflicts are one of the most common causes of service failures.
* Linux networking requires careful attention to YAML formatting and indentation.
* Troubleshooting becomes easier when validating one layer at a time (network, DNS, Kerberos, authentication).

This project successfully demonstrated Linux Active Directory administration, DNS configuration, Kerberos authentication, and Linux client domain integration.
