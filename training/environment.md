# Lab Environment Documentation

## Azure

| Field          | Value             |
|----------------|-------------------|
| Resource group | rg-lab-ad         |
| Region         | France Central    |
| VM size        | Standard_B2als_v2 |
| Auto-shutdown  | 17:00 UTC+1       |

## Domain Controller

| Field          | Value               |
|----------------|---------------------|
| VM name / Hostname | dc01            |
| Private IP     |    10.0.0.4                 |
| OS             | Windows Server 2025 |
| Role           | Domain Controller (pending installation) |
| Domain         | (to be configured)  |
| DSRM password  | stored in password manager |

## Domain Controller

| Field          | Value               |
|----------------|---------------------|
| VM name / Hostname | dc01            |
| Private IP     | (your DC IP)        |
| OS             | Windows Server 2025 |
| Role           | Domain Controller   |
| Domain         | becode.corp.lab          |
| NetBIOS name   | BECODE              |
| Forest level   | Windows Server 2025 |
| DNS zones      | becode.corp.lab, _msdcs.becode.corp.lab |
| SRV records    | Present             |
| DSRM password  | stored in password manager |


A Reverse Lookup Zone in DNS is used to translate an IP address back to a hostname.
cmd - klist
 Client: azureadmin @ BECODE.CORP.LAB
        Server: krbtgt/BECODE.CORP.LAB @ BECODE.CORP.LAB
        KerbTicket Encryption Type: AES-256-CTS-HMAC-SHA1-96
        Ticket Flags 0x40e10000 -> forwardable renewable initial pre_authent name_canonicalize
        Start Time: 3/19/2026 13:02:15 (local)
        End Time:   3/19/2026 23:02:15 (local)
        Renew Time: 3/26/2026 13:02:15 (local)
        Session Key Type: AES-256-CTS-HMAC-SHA1-96
        Cache Flags: 0x1 -> PRIMARY
        Kdc Called: dc01

## Active Directory — Initial State

| Object                | Count / Detail |
|-----------------------|----------------|
| OUs (default)         |     1           |
| Containers (default)  |    5             |
| User accounts         |    1           |
| Security groups       |     19           |
| Domain Admins members | 1 - azureadmin               |

## DNS Verification

| Check                        | Result     |
|------------------------------|------------|
| Forward zone `becode.corp.lab`    | Present    |
| `_msdcs.becode.corp.lab` zone     | Present    |
| Reverse lookup zone          | Yes   |
| SRV records (`_ldap`, etc.)  | OK  |
| `nslookup becode.corp.lab`        | OK  |
| `nslookup dc01.becode.corp.lab`   | OK |

## Kerberos

| Field              | Value |
|--------------------|-------|
| TGT issuer         | azureadmin @ BECODE.CORP.LAB   |
| TGT expiry time    |3/19/2026 23:02:15 (local)    |
| Default TGT lifetime |     |

## Event Log Check

| Log              | Errors found | Notes |
|------------------|--------------|-------|
| Directory Service |       Warning Messages     |  This Active Directory Domain Services server does not support the Recycle Bin. 
Deleted objects may be undeleted, however, when an object is undeleted, some attributes of that object may be lost.
Additionally, attributes of other objects that refer to the object being undeleted may also be lost.     |

DAY 2

## BeCode Corp. — Active Directory Structure

### Departments and OUs
| Department | OU Path |
|---|---|
| Management | OU=Management,OU=Corp,DC=becode,DC=lab |
| Study | OU=Study,OU=Corp,DC=becode,DC=lab |
| Production | OU=Production,OU=Corp,DC=becode,DC=lab |
| Support-A | OU=Support-A,OU=Support,OU=Corp,DC=becode,DC=lab |
| Support-B | OU=Support-B,OU=Support,OU=Corp,DC=becode,DC=lab |
| IT | OU=IT,OU=Corp,DC=becode,DC=lab |
| Service Accounts | OU=ServiceAccounts,OU=Corp,DC=becode,DC=lab |

### Users
| Username | Department | Role | Admin account |
|---|---|---|---|
| claire.dupont | Management | General Manager | claire.admin |
| marc.leroy | Management | Secretary | — |
| thomas.renard | Study | Lead Analyst | — |
| sophie.lambert | Study | Researcher | — |
| julie.martin | Production | Production Supervisor | — |
| kevin.bernard | Production | Operator | — |
| leo.simon | Support-A | Support Agent | — |
| maya.cohen | Support-B | Support Agent | — |
| alice.sysadmin | IT | System Administrator | — |
| svc_backup | ServiceAccounts | Backup service | — |
| svc_ftp | ServiceAccounts | FTP server | — |
| svc_monitor | ServiceAccounts | Monitoring | — |

### Security Groups
| Group | Members |
|---|---|
| Domain Admins | Administrator, claire.admin, alice.sysadmin |
| GRP-IT-Admins | alice.sysadmin |
| GRP-Corp-All | GRP-Management, GRP-Study, GRP-Production, GRP-Support |
| GRP-Helpdesk | alice.sysadmin, leo.simon |

### Accepted risks
| Risk | Severity | Notes |
|---|---|---|
| claire.admin in Domain Admins | High | General Manager has domain admin rights. A targeted attack on her account = full domain compromise. Organizational decision. |
| svc_backup, svc_ftp, svc_monitor — PasswordNeverExpires | Medium | Service accounts. Password rotation requires coordination with application owners. |

## DHCP

| Field               | Value |
|---------------------|-------|
| DHCP Server         | dc01.becode.corp.lab |
| Scope name          | BeCode-Corp-Lab |
| IP range            | 10.0.0.10 - 10.0.0.100
| Subnet mask         | 255.255.255.0      |
| Default gateway     |  10.0.0.1
| DNS server          | (DC private IP) |
| DNS domain          | becode.corp.lab |
| Dynamic DNS updates | Enabled |
## Note — multi-scope production deployment
In a real BeCode Corp. deployment, one scope per department VLAN would be needed.
See network project documentation for VLAN and subnet definitions.

## Security Baseline — BeCode Corp.

### Password Policy (Default Domain Policy)
| Setting                          | Configured value |
|----------------------------------|------------------|
| Minimum password length          |       12           |
| Complexity required              |   Enabled         |
| Maximum password age             |     90 days       |
| Minimum password age             |     1 day        |
| Password history                 |    10 passwords   |

### Account Lockout Policy
| Setting                          | Configured value |
|----------------------------------|------------------|
| Lockout threshold                | 5 valid attempts  |
| Lockout duration                 | 15 minutes    |
| Reset counter after              |   15 minutes    |

### Monitoring (GPO: Security-Monitoring)
| Setting                          | Status |
|----------------------------------|--------|
| PowerShell ScriptBlock Logging   | Turn On|
| PowerShell Module Logging        | Turn On|
| Process Creation Auditing (4688) | Not yet configured — Day 3 |
| Sysmon                           | Not yet installed — Day 3 |
