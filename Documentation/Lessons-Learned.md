# Lessons Learned

## Overview

This project provided hands-on experience building and troubleshooting a Linux-based Active Directory environment.

---

## Key Lessons

### DNS Is Critical

Most Active Directory issues originated from DNS configuration problems.

Without working DNS:

* Domain joins fail
* Kerberos fails
* LDAP discovery fails
* Authentication fails

---

### Kerberos Is Case Sensitive

The following failed:

```text
Administrator@lab.local
```

The following succeeded:

```text
Administrator@LAB.LOCAL
```

---

### Port Conflicts Matter

BIND occupied port 53 and prevented Samba DNS from starting.

Resolution:

```bash
sudo systemctl stop bind9
sudo pkill named
```

---

### Verify One Layer At A Time

The troubleshooting process became much easier by validating:

1. Network Connectivity
2. DNS
3. LDAP
4. Kerberos
5. Domain Authentication

instead of troubleshooting everything at once.

---

### Linux Can Replace Windows For AD Labs

Samba successfully provided:

* Active Directory
* DNS
* Kerberos
* LDAP
* Domain Authentication

without requiring Windows Server.

---

## Skills Gained

### Linux

* System Administration
* Service Management
* Networking
* Troubleshooting

### Active Directory

* Domain Provisioning
* User Management
* Authentication
* Domain Joins

### Networking

* DNS
* DHCP
* Routing
* Name Resolution

### Security

* Kerberos
* Authentication
* Access Control

---

## Future Improvements

* Configure DHCP Integration
* Create Organizational Units
* Create Security Groups
* Join Windows Clients
* Configure Shared Drives
* Implement Group Policies
* Deploy Secondary Domain Controller
* Configure Replication
