# Linux Active Directory Lab

## Overview

This project demonstrates the deployment and management of a Linux-based Active Directory environment using Ubuntu Server, Samba Active Directory Domain Services (AD DS), DNS, Kerberos, and an Ubuntu Desktop client.

The objective of this lab was to gain hands-on experience with identity management, authentication, DNS, domain services, and Linux system administration while simulating a real-world enterprise environment.

---

## Project Objectives

* Deploy a Linux-based Active Directory Domain Controller
* Configure Samba AD DS
* Configure DNS services
* Configure Kerberos authentication
* Join a Linux client to the domain
* Validate domain authentication and directory services
* Troubleshoot common Active Directory and DNS issues
* Document deployment and troubleshooting procedures

---

## Lab Environment

### Domain Controller

| Component         | Value                  |
| ----------------- | ---------------------- |
| Operating System  | Ubuntu Server          |
| Hostname          | ubuntu01               |
| IP Address        | 192.168.64.4           |
| Domain            | lab.local              |
| Realm             | LAB.LOCAL              |
| Directory Service | Samba Active Directory |

### Client

| Component         | Value               |
| ----------------- | ------------------- |
| Operating System  | Ubuntu Desktop      |
| Hostname          | client01            |
| Authentication    | Kerberos            |
| Domain Membership | Joined to LAB.LOCAL |

---

## Technologies Used

* Ubuntu Server
* Ubuntu Desktop
* Samba Active Directory Domain Services
* Kerberos
* DNS
* SSSD
* Realmd
* LDAP
* Netplan
* Linux Networking

---

## Skills Demonstrated

### Active Directory Administration

* Domain provisioning
* User management
* Domain authentication
* Directory services

### Linux Administration

* Service management
* Network configuration
* DNS troubleshooting
* System configuration

### Networking

* DNS configuration
* DHCP concepts
* Routing
* Name resolution

### Security

* Kerberos authentication
* Access control
* Identity management

---

## Project Architecture

```text
                 Internet
                     |
                     |
              UTM Shared Network
                 192.168.64.1
                     |
        --------------------------------
        |                              |
        |                              |
 Ubuntu Domain Controller      Ubuntu Client
      ubuntu01                   client01
    192.168.64.4                DHCP
         |                         |
         |                         |
         +------ LAB.LOCAL --------+
```

---

## Major Challenges Encountered

During the deployment process, several real-world issues were encountered and resolved:

* Netplan YAML formatting errors
* DNS zone configuration errors
* Samba provisioning failures
* Port 53 conflicts caused by BIND
* Samba DNS binding issues
* Kerberos authentication failures
* Domain discovery failures
* Client DNS misconfiguration

Detailed troubleshooting steps can be found in:

```text
Documentation/Troubleshooting.md
```

---

## Validation

The following tests were successfully completed:

### Active Directory Domain Controller

```bash
sudo samba-tool domain level show
```

### DNS Resolution

```bash
host -t SRV _ldap._tcp.lab.local 192.168.64.4
```

### Kerberos Authentication

```bash
kinit Administrator@LAB.LOCAL
```

### Domain Membership

```bash
realm list
```

Result:

```text
realm-name: LAB.LOCAL
configured: kerberos-member
server-software: active-directory
client-software: sssd
```

---

## Repository Structure

```text
Linux-Active-Directory-Lab/
│
├── README.md
│
├── Documentation/
│   ├── Setup-Guide.md
│   ├── Troubleshooting.md
│   ├── Network-Diagram.md
│   └── Lessons-Learned.md
│
├── Screenshots/
│   ├── 01-Domain-Provisioned.png
│   ├── 02-Samba-Running.png
│   ├── 03-DNS-Working.png
│   ├── 04-Kerberos-Ticket.png
│   ├── 05-Client-Joined-Domain.png
│   └── 06-Domain-Authentication.png
│
└── Configs/
    ├── README.md
    ├── smb.conf
    ├── dhcpd.conf
    └── 00-installer-config.yaml
```

---

## Key Takeaways

This project provided practical experience with enterprise identity services and reinforced the importance of DNS, Kerberos, and proper network configuration within Active Directory environments.

The successful deployment of a Samba Active Directory Domain Controller and domain-joined Linux client demonstrates foundational system administration, troubleshooting, and infrastructure management skills applicable to Help Desk, System Administration, and IT Support roles.

---

## Future Improvements

* Deploy a Windows 11 domain-joined client
* Configure DHCP integration
* Create Organizational Units (OUs)
* Create Security Groups
* Configure shared network drives
* Deploy a secondary Domain Controller
* Implement replication testing
* Explore Group Policy alternatives for Linux systems
