# MTWAzureLab Active Directory Home Lab

> Windows Server 2025 • Microsoft Azure • Active Directory Domain Services • PowerShell

## Overview

This project demonstrates the deployment and configuration of a fully functional Active Directory environment hosted in Microsoft Azure using Windows Server 2025.

The lab simulates a small business environment by creating departmental Organizational Units (OUs), Security Groups, User Accounts, and Group Policy Objects (GPOs). The goal is to showcase foundational Identity and Access Management (IAM) skills commonly used by Help Desk Technicians, System Administrators, Cloud Engineers, and Security Analysts.

---

# Business Problem

Organizations need a centralized way to manage users, computers, permissions, and security policies.

Active Directory solves this problem by providing:

- Centralized user management
- Authentication and authorization
- Role-Based Access Control (RBAC)
- Group Policy enforcement
- Simplified administration of enterprise environments

Instead of configuring each computer individually, administrators can manage users and security policies from a single location.

---

# Technologies Used

| Technology | Purpose |
|------------|----------|
| Windows Server 2025 | Domain Controller |
| Microsoft Azure | Virtual Infrastructure |
| Active Directory Domain Services (AD DS) | Identity Management |
| Group Policy Management Console (GPMC) | Policy Enforcement |
| PowerShell | Automation |
| Active Directory Users and Computers (ADUC) | Directory Administration |

---

# Lab Architecture

## Domain Information

| Setting | Value |
|----------|----------|
| Domain Name | MTWAzureLab.local |
| Forest Name | MTWAzureLab.local |
| Domain Controller | Windows Server 2025 |
| Platform | Microsoft Azure |

---

# Organizational Structure

## Organizational Units (OUs)

| OU Name |
|----------|
| IT |
| Sales |
| HR |
| Finance |
| Computers |

## Security Groups

| Department | Group Name |
|------------|------------|
| IT | IT Admins |
| Sales | Sales Users |
| HR | HR Users |
| Finance | Finance Users |

## User Accounts

| Department | User |
|------------|----------|
| IT | Naomi Vale |
| Sales | Julian Mercer |
| HR | Elena Cross |
| Finance | Mason Whitaker |

---

# Step 1 - Install Active Directory Domain Services

## Why This Matters

Active Directory cannot function until the AD DS role is installed. This role provides the services required to manage users, groups, computers, and authentication within a Windows domain.

### Install AD DS

```powershell
Install-WindowsFeature -Name AD-Domain-Services -IncludeManagementTools
```

### Install Group Policy Management Console

```powershell
Install-WindowsFeature -Name GPMC
```

---

## Screenshot

### SC1 - AD DS Installation Complete

Insert a screenshot showing:

- Active Directory Domain Services installed
- Installation results successful
- Server Manager confirmation screen

Example:

```text
/screenshots/SC1-ADDS-Installed.png
```

---

# Step 2 - Promote the Server to a Domain Controller

## Why This Matters

Installing AD DS only installs the software.

Promoting the server creates:

- Active Directory Forest
- Active Directory Domain
- DNS Infrastructure
- Authentication Services

This transforms the server into the identity authority for the environment.

## Configuration

| Setting | Value |
|----------|----------|
| Forest Type | New Forest |
| Domain Name | MTWAzureLab.local |

### Promotion Steps

1. Open Server Manager
2. Click the notification flag
3. Select **Promote this server to a domain controller**
4. Select **Add a new forest**
5. Enter:

```text
MTWAzureLab.local
```

6. Configure DSRM password
7. Complete the wizard
8. Reboot the server

---

## Screenshot

### SC2 - Domain Controller Promotion

Insert a screenshot showing:

- New Forest selected
- MTWAzureLab.local configured

Example:

```text
/screenshots/SC2-Domain-Promotion.png
```

---

# Step 3 - Create Organizational Units

## Why This Matters

Organizational Units (OUs) provide logical separation between departments.

This allows administrators to:

- Organize users
- Organize computers
- Apply department-specific Group Policies
- Simplify administration

### PowerShell

```powershell
New-ADOrganizationalUnit -Name "IT" -Path "DC=MTWAzureLab,DC=local"

New-ADOrganizationalUnit -Name "Sales" -Path "DC=MTWAzureLab,DC=local"

New-ADOrganizationalUnit -Name "HR" -Path "DC=MTWAzureLab,DC=local"

New-ADOrganizationalUnit -Name "Finance" -Path "DC=MTWAzureLab,DC=local"

New-ADOrganizationalUnit -Name "Computers" -Path "DC=MTWAzureLab,DC=local"
```

---

## Screenshot

### SC3 - Organizational Units Created

Insert a screenshot showing:

- IT OU
- Sales OU
- HR OU
- Finance OU
- Computers OU

inside Active Directory Users and Computers.

Example:

```text
/screenshots/SC3-Organizational-Units.png
```

---

# Step 4 - Create Security Groups

## Why This Matters

Security Groups enable Role-Based Access Control (RBAC).

Instead of assigning permissions to individual users, permissions are assigned to groups.

This approach scales much better in enterprise environments.

### PowerShell

```powershell
New-ADGroup -Name "IT Admins" -GroupScope Global -GroupCategory Security -Path "OU=IT,DC=MTWAzureLab,DC=local"

New-ADGroup -Name "Sales Users" -GroupScope Global -GroupCategory Security -Path "OU=Sales,DC=MTWAzureLab,DC=local"

New-ADGroup -Name "HR Users" -GroupScope Global -GroupCategory Security -Path "OU=HR,DC=MTWAzureLab,DC=local"

New-ADGroup -Name "Finance Users" -GroupScope Global -GroupCategory Security -Path "OU=Finance,DC=MTWAzureLab,DC=local"
```

---

## Screenshot

### SC4 - Security Groups Created

Insert a screenshot showing:

- IT Admins
- Sales Users
- HR Users
- Finance Users

Example:

```text
/screenshots/SC4-Security-Groups.png
```

---

# Step 5 - Create User Accounts

## Why This Matters

User accounts represent employees within the organization.

Authentication, permissions, and resource access are tied directly to user accounts and their group memberships.

### Password Variable

```powershell
$password = ConvertTo-SecureString "Welcome@2026!" -AsPlainText -Force
```

### Users Created

| User | Department |
|--------|------------|
| Naomi Vale | IT |
| Julian Mercer | Sales |
| Elena Cross | HR |
| Mason Whitaker | Finance |

### Add Users to Security Groups

```powershell
Add-ADGroupMember -Identity "IT Admins" -Members "naomi.vale"

Add-ADGroupMember -Identity "Sales Users" -Members "julian.mercer"

Add-ADGroupMember -Identity "HR Users" -Members "elena.cross"

Add-ADGroupMember -Identity "Finance Users" -Members "mason.whitaker"
```

---

## Screenshot

### SC5 - User Accounts Created

Insert a screenshot showing:

- Naomi Vale
- Julian Mercer
- Elena Cross
- Mason Whitaker

within their respective OUs.

Example:

```text
/screenshots/SC5-Users-Created.png
```

---

# Step 6 - Configure Group Policy

## Why This Matters

Group Policy allows administrators to enforce security settings across all domain devices from a central location.

Without Group Policy, each workstation would require manual configuration.

## GPO Name

```text
MTWAzureLab Security Policy
```

## Policies Configured

| Policy | Value | Business Benefit |
|----------|----------|----------|
| Minimum Password Length | 12 | Strong passwords |
| Password Complexity | Enabled | Improved credential security |
| Machine Inactivity Limit | 900 Seconds | Prevents unauthorized access |
| USB Storage Access | Disabled | Prevents data exfiltration |

---

## Screenshot

### SC6 - Minimum Password Length

Insert screenshot of:

```text
Computer Configuration
 └ Security Settings
     └ Password Policy
         └ Minimum Password Length
```

```text
/screenshots/SC6-Minimum-Password-Length.png
```

---

### SC7 - Password Complexity Enabled

Insert screenshot of:

```text
Password must meet complexity requirements
```

```text
/screenshots/SC7-Password-Complexity.png
```

---

### SC8 - Machine Inactivity Limit

Insert screenshot of:

```text
Interactive Logon: Machine Inactivity Limit
```

```text
/screenshots/SC8-Inactivity-Limit.png
```

---

### SC9 - USB Storage Restriction

Insert screenshot of:

```text
All Removable Storage Classes:
Deny All Access
```

```text
/screenshots/SC9-USB-Restriction.png
```

---

# Step 7 - Common Help Desk Tasks

These are common Active Directory tasks performed daily by Help Desk and System Administration teams.

## Reset a Password

```powershell
Set-ADAccountPassword -Identity "naomi.vale" -Reset `
-NewPassword (ConvertTo-SecureString "NewPass@2026!" -AsPlainText -Force)

Set-ADUser -Identity "naomi.vale" -ChangePasswordAtLogon $true
```

## Unlock an Account

```powershell
Unlock-ADAccount -Identity "julian.mercer"
```

## Disable an Account

```powershell
Disable-ADAccount -Identity "elena.cross"
```

## Audit Group Membership

```powershell
Get-ADPrincipalGroupMembership -Identity "mason.whitaker"
```

---

# Step 8 - Verification

## Why This Matters

Verification confirms the environment is functioning correctly and all Active Directory objects were created successfully.

### Verify Domain Controller

```powershell
Get-ADDomainController
```

### Verify Organizational Units

```powershell
Get-ADOrganizationalUnit -Filter *
```

### Verify Users

```powershell
Get-ADUser -Filter *
```

### Verify Group Memberships

```powershell
Get-ADGroupMember -Identity "IT Admins"
```

### Verify Group Policy

```powershell
Get-GPInheritance -Target 'OU=IT,DC=MTWAzureLab,DC=local'
```

---

## Screenshot

### SC10 - Verification Results

Insert a screenshot showing:

- Successful PowerShell verification
- Existing OUs
- Existing Users
- Existing Groups

Example:

```text
/screenshots/SC10-Verification.png
```

---

# Common Troubleshooting

| Problem | Solution |
|-----------|-----------|
| User creation script fails | Ensure `$password` variable is created first |
| Cannot copy/paste to Azure VM | Enable clipboard redirection in RDP |
| Promotion fails | Verify DNS configuration |
| GPO not applying | Run `gpupdate /force` |
| Cannot log into domain | Verify user account is enabled |
| ADUC missing | Run `dsa.msc` |

---

# Skills Demonstrated

- Active Directory Administration
- Azure Infrastructure Management
- Windows Server Administration
- PowerShell Automation
- Group Policy Administration
- Identity and Access Management (IAM)
- Role-Based Access Control (RBAC)
- Help Desk Troubleshooting
- Security Policy Enforcement

---

# Key Takeaways

This lab demonstrates the complete deployment of an Active Directory environment in Microsoft Azure, including domain creation, organizational design, user provisioning, security group management, Group Policy implementation, and administrative troubleshooting.

The skills practiced in this project directly align with responsibilities commonly found in:

- Help Desk
- Desktop Support
- Systems Administration
- Cloud Administration
- Cybersecurity
- Identity and Access Management (IAM)
