# configure-ad
Hands-on Active Directory lab using Microsoft Azure, Windows Server 2022, Group Policy, and PowerShell to deploy, configure, and manage a Windows domain environment.


<p align="center">
  <img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo" width="180"/>
</p>

<h1 align="center">Active Directory Deployment and Administration Lab</h1>

<p align="center">
  <strong>Microsoft Azure | Windows Server 2022 | Active Directory | PowerShell</strong>
</p>

---

## Project Overview

This project demonstrates the deployment and administration of an **Active Directory Domain Services (AD DS)** environment hosted on Microsoft Azure virtual machines.

The lab focuses on configuring a Windows Server domain controller, establishing communication between virtual machines, implementing centralized user management, and configuring domain-based authentication.

Additional tasks include managing Organizational Units (OUs), configuring administrative privileges, enabling Remote Desktop access through Group Policy, and automating user account creation using PowerShell.

The objective is to simulate real-world IT infrastructure administration tasks commonly performed in enterprise environments.

---

## Implementation Overview

1. Provision Windows Server and Windows client virtual machines in Microsoft Azure.
2. Establish network connectivity between the domain controller and client workstation.
3. Install Active Directory Domain Services and configure a new domain.
4. Create Organizational Units and administrative user accounts.
5. Join the Windows client to the Active Directory domain.
6. Configure Remote Desktop access using Group Policy.
7. Automate user account creation with PowerShell.
8. Verify domain authentication and successful configuration.

---

## Technologies and Tools

- **Microsoft Azure:** Virtual Machines, Resource Groups, Virtual Networks
- **Active Directory:** Active Directory Domain Services (AD DS)
- **Administration:** Server Manager, Active Directory Users and Computers (ADUC)
- **Networking:** DNS, Private IP Addressing, ICMP, Windows Defender Firewall
- **Remote Access:** Remote Desktop Protocol (RDP)
- **Policy Management:** Group Policy Management Console (GPMC)
- **Automation:** Windows PowerShell ISE

## Operating Systems

- Windows Server 2022 — Domain Controller
- Windows 10 (21H2) — Client Workstation

---

## Phase 1: Provision Azure Infrastructure

The first phase involves deploying the virtual infrastructure needed to establish an Active Directory environment.

### Configuration Steps

1. Deploy a Windows Server 2022 virtual machine named **DC-1**.
2. Identify the Resource Group and Virtual Network associated with DC-1.
3. Configure DC-1's network interface with a static private IP address.
4. Deploy a Windows 10 virtual machine named **Client-1**.
5. Place Client-1 within the same Resource Group and Virtual Network as DC-1.
6. Verify the network configuration using Azure networking tools.

**Expected Result:** Both virtual machines are deployed within the same Azure network environment and are ready for domain configuration.

---

## Phase 2: Verify Network Connectivity

Before deploying Active Directory, network communication between the domain controller and client must be established.

### Configuration Steps

1. Connect to Client-1 using Remote Desktop.
2. Open Command Prompt.
3. Initiate a continuous ping to DC-1's private IP address:

```powershell
ping -t <DC-1_Private_IP>
```

4. Connect to DC-1 using Remote Desktop.
5. Open Windows Defender Firewall settings.
6. Enable the inbound ICMPv4 echo request firewall rule.
7. Return to Client-1 and confirm successful ping responses.

**Expected Result:** Client-1 successfully communicates with DC-1 over the private Azure network.

---

## Phase 3: Install Active Directory Domain Services

This phase establishes the centralized directory infrastructure used to manage domain users, computers, and authentication.

### Configuration Steps

1. Sign in to DC-1 using administrator credentials.
2. Open **Server Manager**.
3. Select **Add Roles and Features**.
4. Install the **Active Directory Domain Services (AD DS)** role.
5. Promote DC-1 to a domain controller.
6. Create a new Active Directory forest using the domain:

```text
mydomain.com
```

7. Complete the installation and restart DC-1.
8. Sign in using the appropriate domain administrator credentials.

**Expected Result:** DC-1 operates as a domain controller and provides centralized authentication and directory management services.

---

## Phase 4: Configure Organizational Units and Administrative Accounts

After establishing the domain, configure a structured Active Directory environment for managing users and computers.

### Create Organizational Units

Open **Active Directory Users and Computers (ADUC)** and create the following OUs:

| Organizational Unit | Purpose |
|---------------------|---------|
| `_EMPLOYEES` | Stores standard employee accounts |
| `_ADMINS` | Contains administrative user accounts |
| `_CLIENTS` | Organizes domain-joined workstations |

### Create an Administrative Account

1. Navigate to the `_ADMINS` Organizational Unit.
2. Create a new user with the following information:

| Attribute | Value |
|-----------|-------|
| Display Name | Jane Doe |
| Username | `jane_admin` |
| Account Type | Domain Administrator |

3. Add `jane_admin` to the **Domain Admins** security group.
4. Sign out of the current session.
5. Sign in using the newly created administrative account.

```text
mydomain.com\jane_admin
```

**Expected Result:** Active Directory contains a structured OU hierarchy and a dedicated administrative account.

> **Security Note:** Domain Admins membership grants extensive privileges. In production environments, administrative access should follow the principle of least privilege.

---

## Phase 5: Join Client-1 to the Active Directory Domain

This phase integrates the Windows client into the domain to enable centralized authentication and computer management.

### Configuration Steps

1. Open the Azure Portal.
2. Navigate to Client-1's network configuration.
3. Configure the client's DNS server to use DC-1's private IP address.
4. Restart Client-1 to apply the DNS changes.
5. Connect to Client-1 using the local administrator account.
6. Open Windows system settings.
7. Initiate the domain-join process.
8. Enter the domain name:

```text
mydomain.com
```

9. Provide authorized domain credentials.
10. Restart Client-1.
11. Open ADUC on DC-1.
12. Verify that Client-1 appears in the **Computers** container.
13. Move Client-1 into the `_CLIENTS` Organizational Unit.

**Expected Result:** Client-1 successfully joins the Active Directory domain and can be centrally managed.

---

## Phase 6: Configure Remote Desktop Access Using Group Policy

Group Policy allows administrators to centrally configure Remote Desktop settings across domain-joined computers.

### Configuration Steps

1. Sign in to DC-1 using an administrative account.
2. Open the **Group Policy Management Console (GPMC)**.
3. Create a new Group Policy Object for Remote Desktop configuration.
4. Link the GPO to the `_CLIENTS` Organizational Unit.
5. Configure the policy to allow Remote Desktop connections.
6. Enable the necessary Windows Defender Firewall rules.
7. Assign Remote Desktop access permissions to an authorized group of standard domain users.
8. On Client-1, open Command Prompt as an administrator.
9. Run the following command:

```powershell
gpupdate /force
```

10. Verify that the Group Policy settings are applied.
11. Test Remote Desktop access using an authorized standard domain user.

**Expected Result:** Authorized non-administrative users can establish Remote Desktop sessions with Client-1 through centrally managed policies.

> **Security Note:** Remote Desktop permissions should only be granted to users who require access. Computer-based Group Policy settings must be linked to the appropriate computer OU.

---

## Phase 7: Automate Active Directory User Creation with PowerShell

This phase demonstrates how PowerShell can streamline administrative tasks by automating the creation of multiple Active Directory accounts.

### Configuration Steps

1. Sign in to DC-1 using the `jane_admin` account.
2. Open **Windows PowerShell ISE** as an administrator.
3. Execute a PowerShell script designed to create multiple Active Directory user accounts.
4. Configure the script to assign users to the `_EMPLOYEES` Organizational Unit.
5. Monitor script execution and review any errors.
6. Open Active Directory Users and Computers.
7. Confirm that the newly created accounts appear in the correct OU.
8. Select one of the newly created accounts.
9. Attempt to sign in to Client-1 using the selected domain credentials.
10. Verify successful domain authentication.

**Expected Result:** Multiple Active Directory accounts are provisioned through PowerShell automation, and their functionality is verified through successful client authentication.

---

## Project Validation

The following checks are used to confirm successful implementation:

- [x] Windows Server and Windows client virtual machines deployed in Azure
- [x] Network connectivity established between DC-1 and Client-1
- [x] Active Directory Domain Services installed and configured
- [x] Organizational Units created and organized
- [x] Administrative user account configured
- [x] Client-1 joined to the Active Directory domain
- [x] Group Policy configured for Remote Desktop access
- [x] PowerShell used to automate account creation
- [x] New domain user accounts verified in Active Directory
- [x] Domain authentication tested on Client-1

---

## Skills Demonstrated

This project demonstrates practical experience with:

- Windows Server 2022 administration
- Microsoft Azure virtual machine deployment
- Active Directory Domain Services
- Domain controller configuration
- Organizational Unit management
- User account provisioning and administration
- Security group management
- DNS configuration and troubleshooting
- Windows Defender Firewall configuration
- Group Policy administration
- Remote Desktop configuration
- PowerShell automation
- Domain authentication
- Virtual infrastructure management

---

## Conclusion

This project demonstrates the deployment and configuration of a centralized Active Directory environment using Microsoft Azure infrastructure.

The implementation included provisioning virtual machines, establishing network connectivity, deploying a Windows Server domain controller, and integrating a Windows client into the domain.

Additional administrative responsibilities included configuring Organizational Units, managing privileged accounts, implementing Remote Desktop access through Group Policy, and automating user account creation using PowerShell.

The lab provided practical experience with **Windows Server administration, identity and access management, network troubleshooting, and IT infrastructure automation**.


---

## Future Improvements

Potential enhancements to this environment include:

- Implementing automated user onboarding and offboarding
- Configuring additional Group Policy security settings
- Deploying shared folders with NTFS permissions
- Implementing Windows Server backup and recovery
- Expanding PowerShell automation
- Integrating Microsoft Entra ID with on-premises Active Directory
- Implementing centralized monitoring and event logging




