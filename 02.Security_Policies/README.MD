# Active Directory Security Hardening Lab

## Project Overview

This project focuses on implementing a structured security baseline in an Active Directory environment using Group Policy and PowerShell.

The objective was not just to configure settings, but to design them in a controlled and scalable way. Instead of applying changes directly to default configurations, policies were separated, scoped, and validated to reflect how enterprise environments are managed.

---

## Objectives

- Implement a structured Active Directory security baseline  
- Apply Group Policy in a controlled and scalable way  
- Separate policies based on system roles (Domain, Domain Controllers, Workstations)  
- Enforce modern security practices (disable legacy protocols, strengthen authentication)  
- Use PowerShell to automate configuration  
- Validate policy application and troubleshoot issues  

---

## Environment / Architecture

- Domain: `northbridge.local`  
- Domain Controller: Windows Server 2022  
- Client Machine: Windows 11  

Technologies used:

- Active Directory Domain Services (AD DS)  
- Group Policy Management  
- PowerShell  

This setup was chosen to simulate a small enterprise environment where policies are centrally managed and applied across different types of systems.

---

## Design Decisions

### GPO Separation

Separate GPOs were created instead of modifying the Default Domain Policy:

- Domain Security  
- Domain Controller Security  
- Workstation Security  

This reduces risk and makes it easier to manage and troubleshoot policies independently.

---

### OU-Based Targeting

Policies were applied based on Organizational Units.

This ensures that each type of system receives only the configurations relevant to it, avoiding unintended impact.

---

### PowerShell Automation

PowerShell was used to create and manage policies.

This allows repeatability, reduces manual errors, and reflects how configurations are handled in real environments.

---

## Implementation

### 1. Load Required Modules

PowerShell modules for Active Directory and Group Policy were loaded to enable automation and scripting.

![Modules Loaded](images/01-modules-loaded.png)

---

### 2. Define Domain Paths

Distinguished Names and OU paths were defined to ensure that all objects and policies are created in the correct locations.

![Domain Paths](images/02-domain-paths.png)

---

### 3. Create Security GPOs

GPOs were created to separate security controls by scope, allowing each layer of the environment to be managed independently.

![GPO Created](images/03.00-gpo-created.png)

---

### 4. Validate GPO Linking

An incorrect GPO link was initially applied, which prevented policies from working as expected.

![GPO Link Issue](images/03.01-gpo-linkmistakeworkstation.png)

After correcting the target, the policy applied properly:

![GPO Link Fixed](images/03.02-workstations-gpo-linkedsolved.png)

This highlights the importance of verifying scope after configuration.

---

### 5. Link GPOs to Correct Targets

GPOs were linked to the appropriate OUs to ensure correct application across Domain, Domain Controllers, and Workstations.

![GPO Links](images/04-gpo-links.png)

---

### 6. Configure Domain Password Policy

A domain-level password policy was configured to enforce baseline security requirements such as password length, history, and lockout settings.

![Domain Password Policy](images/05-domainpassword-policy.png)

---

### 7. Create Fine-Grained Password Policy

A stricter password policy was created specifically for privileged accounts.

This addresses the limitation of domain policies applying equally to all users.

![FGPP Created](images/06-fgpp-created.png)

---

### 8. Apply FGPP to Administrative Accounts

The stricter policy was applied to privileged users to ensure stronger authentication controls for high-risk accounts.

![FGPP Applied](images/07-fgpp-applied.png)

---

### 9. Disable SMBv1

SMBv1 was disabled to remove a legacy protocol with known vulnerabilities, reducing the attack surface.

![SMB Disabled](images/08-smb-disabled.png)

---

### 10. Enable Firewall via GPO

Firewall settings were enforced centrally using Group Policy to ensure consistency across all systems.

![Firewall Enabled](images/09-firewall-enabled.png)

---

## Validation / Testing

After applying the configurations, policies were verified using system tools and PowerShell:


gpresult /r
Get-ADFineGrainedPasswordPolicy
Get-NetFirewallProfile
Get-WindowsOptionalFeature -Online -FeatureName SMB1Protocol

Validation ensures that policies are not only configured, but actually enforced.

## Key Concepts Applied
Group Policy Management
Identity and Access Management (IAM)
Role-Based Access Control (RBAC)
Least Privilege Principle
Security Hardening

## What I Learned

This project made it clear that security in Active Directory depends more on structure than individual configurations.

Separating policies by scope improved visibility and control. It also highlighted how easy it is for small mistakes, such as incorrect GPO linking, to affect the environment without obvious errors.

I also gained a better understanding of how to apply different security levels depending on the type of account, especially for administrative users.

## Real-World Relevance

This type of configuration is commonly used in enterprise environments where centralized control and consistent security are required.

System administrators rely on Group Policy to enforce standards, reduce configuration drift, and manage large environments efficiently.

## Skills Demonstrated
Active Directory administration
Group Policy configuration
PowerShell automation
Troubleshooting GPO issues
Security hardening
Technologies Used
Windows Server 2022
Windows 11
Active Directory Domain Services
Group Policy Management
PowerShell


