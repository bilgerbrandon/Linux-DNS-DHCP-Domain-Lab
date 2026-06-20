# Network Diagram

## Logical Layout

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

## Domain Controller Services

### DNS

Port:

```text
53
```

Provides:

* Domain Name Resolution
* Active Directory SRV Records
* Kerberos Discovery

---

### Kerberos

Port:

```text
88
```

Provides:

* Authentication
* Ticket Granting
* Secure Logins

---

### LDAP

Port:

```text
389
```

Provides:

* Directory Services
* User Management
* Domain Discovery

---

## Authentication Flow

1. Client queries DNS.
2. DNS returns LDAP and Kerberos records.
3. Client requests Kerberos ticket.
4. Domain Controller validates credentials.
5. Ticket is issued.
6. Client gains domain access.

---

## Active Directory Components

### Domain

```text
lab.local
```

### Realm

```text
LAB.LOCAL
```

### Domain Controller

```text
ubuntu01.lab.local
```

### Client

```text
client01.lab.local
```
