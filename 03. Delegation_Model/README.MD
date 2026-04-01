# Active Directory Delegation Model — Northbridge

## Project Overview

This project focuses on designing and implementing a structured **delegation model in Active Directory** using a simulated enterprise environment.

In many organizations, operational tasks like password resets or user updates are handled by Domain Admins. This creates unnecessary risk and makes the environment harder to scale.

In this lab, I redesigned that approach by distributing responsibilities across different roles (Helpdesk, HR, and Managers) using **group-based delegation, OU scoping, and PowerShell automation**.

The result is a cleaner, more secure, and more realistic identity management model aligned with how enterprise environments operate.

---

## Objectives

- Implement Role-Based Access Control (RBAC) in Active Directory  
- Delegate permissions using security groups instead of users  
- Apply least privilege across operational roles  
- Use PowerShell (dsacls) for repeatable configuration  
- Validate both successful and denied scenarios  
- Collect audit evidence from domain controller logs  

---

## Environment / Architecture

- **Domain:** northbridge.local  
- **Domain Controller:** Windows Server 2022  
- **Client:** Windows 11 (joined to domain with RSAT installed)  

### Tools Used

- Active Directory Users and Computers (ADUC)  
- PowerShell (ActiveDirectory module)  
- DSACLS  
- Windows Security Logs  

### How the environment was used

- The **client machine** was used to simulate real user/admin activity (Helpdesk, HR, Managers)  
- The **domain controller** was used for:
  - applying delegation  
  - validating permissions  
  - collecting audit logs  

This separation reflects how things actually work in production environments.

---

## Design Decisions

### OU Structure

Users are organized under:

- `OU=1.NB_Users`
- `OU=HR` (scoped for HR operations)
- `OU=DisabledUsers` (for offboarding)

Permissions were applied at the OU level to control exactly where each role can operate.

---

### Group-Based Delegation

All permissions were assigned to **delegation groups (DLG_*)**, not individual users.

Examples:

- DLG_Helpdesk_Reset_Users  
- DLG_HR_Modify_Users  
- DLG_Managers_Read_Departments  

This makes the model easier to maintain and much cleaner when validating or troubleshooting.

---

### Separation of Duties

Each role has a clear responsibility:

- Helpdesk → operational support  
- HR → identity lifecycle  
- Managers → read-only visibility  

This prevents privilege overlap and keeps responsibilities clearly defined.

---

## Implementation

### 1: Initial AD Structure Validation

Before applying any delegation, I verified that OUs, users, and attributes were correctly created.

![AD Structure](images/01-ad-current-structure.png)

---

### 2: Delegation Groups OU

Created a dedicated OU for delegation groups to separate them from business roles.

![Delegation OU](images/02-delegation-groups-ou.png)

---

### 3: Delegation Groups Creation

Created all DLG_* groups using a consistent naming convention.

![Groups Created](images/03-delegation-groups-created.png)

---

### 4: Group Membership Assignment

Assigned accounts like:

- adm.helpdesk01 → Helpdesk groups  
- mlopez → HR groups  
- managers → read-only groups  

![Group Membership](images/04-delegation-group-membership.png)

---

### 5: Naming Standard Validation

Confirmed that all delegation groups follow the same naming pattern.

![Naming Standard](images/05-delegation-naming-standard.png)

---

### 6: Helpdesk Password Reset Delegation

Delegated password reset capability at the user OU level using dsacls.

![Reset Delegation](images/06-helpdesk-reset-password-delegation.png)

---

### 7: Helpdesk Contact Attribute Delegation

Allowed Helpdesk to update operational attributes like phone and description.

![Attributes](images/07-helpdesk-contact-attributes-delegation.png)

---

### 10:Helpdesk — Join Computers Delegation

During the delegation process, an initial attempt was made to assign validated write permissions for DNS and Service Principal Name (SPN) attributes using `dsacls`.

![Validation Error](images/08-0-validatewrerror.png)

This resulted in an error indicating that these attributes require **Write Self (WS)** permissions, which cannot be assigned using standard control access (`CA`) entries.

Instead of forcing a low-level attribute delegation, the approach was adjusted to follow a more practical and enterprise-aligned model.

The Helpdesk group was granted permissions at the object level, allowing them to manage computer objects directly within the OU.

![Delegation Applied](images/08-1-helpdesk-join-computers-delegation.png)

The delegation grants the following capabilities:

- Create computer objects in the domain  
- Delete computer objects when needed  

This aligns with how Helpdesk operations typically work in real environments, where the focus is on managing objects rather than modifying internal attributes.

To confirm that the delegation was correctly applied, the permissions were validated using `dsacls` filtering.

![Delegation Validation](images/08-2-helpdesk-join-computers-delegation-filter.png)

The output confirms that the Helpdesk delegation group has:

- `CREATE CHILD` (CC) — ability to create computer objects  
- `DELETE CHILD` (DC) — ability to remove computer objects  

This ensures that Helpdesk users can successfully join machines to the domain without requiring Domain Admin privileges, while still keeping control scoped and secure.

---

### 9: Helpdesk Read Login Attributes

Allowed read-only access to attributes used for troubleshooting login issues.

![Read Login](images/09-helpdesk-read-login-attributes.png)

---

### 10: Helpdesk Move Users

Delegated the ability to move users between OUs.

![Move Users](images/10-helpdesk-move-users-delegation.png)

---

### 11: HR Modify Users

HR can update:

- Title  
- Department  
- Manager  
- Description  

![HR Modify](images/11-hr-modify-users-delegation.png)

---

### 12: HR Disable Accounts

Delegated the ability to disable user accounts for offboarding.

![HR Disable](images/12-hr-disable-users-delegation.png)

---

### 13: HR Create and Delete Users

Allowed HR to create and delete users within their scoped OU.

![HR Create](images/13-hr-create-delete-users.png)

---

### 14: HR Move Disabled Users

Created a DisabledUsers OU and delegated movement into it.

![Disabled Users](images/14-hr-move-disabled-users.png)

---

### 15: Manager Read Attributes

Managers can view key attributes like department and manager relationships.

![Manager Read](images/15-manager-read-attributes.png)

---

### 16: Manager Read Group Membership

Managers can validate group membership without modifying it.

![Group Membership](images/16-manager-read-group-membership.png)

---

## Validation / Testing

### Helpdesk Tests (Client Machine)

Logged in as:
northbridge\adm.helpdesk01


Validated:

- Password reset → worked  
- Reading attributes → worked  
- Changing department → denied  

![Helpdesk Success](images/17-validation-helpdesk-success.png)  
![Helpdesk Denied](images/18-validation-helpdesk-denied.png)

---

### HR Tests (Client Machine)

Logged in as:
northbridge\mlopez


Validated:

- Modify Title/Department → worked  
- Disable account → worked  
- Reset password → denied  

During testing, creating users sometimes returned **“Access Denied”** but the user was still created.  
This helped confirm that permissions were partially correct and required careful validation.

![HR Success](images/19-validation-hr-success.png)  
![HR Denied](images/20-validation-hr-denied.png)

---

### Manager Read-Only Validation

Managers were able to read:

- direct reports  
- department structure  

![Manager Read](images/21-validation-manager-readonly.png)

An additional validation was performed attempting to modify user attributes, which resulted in an access denied error.

This confirmed that the role is properly restricted to read-only access.

![Manager Unauthorized Attempt](images/21-01-validation-manager-unauth-readonly.png)

---

### Audit Validation (Domain Controller)

Audit logs were collected directly from the domain controller.

Password reset events:

![Audit Reset](images/23-audit-password-reset-event.png)

Additional events validated:

- Account disabled  
- User changes  

This confirmed that delegated actions are properly logged.

---

### Automation Readiness

Validated that all users contain structured attributes:

- Department  
- Title  
- Manager  

![Automation](images/26-automation-readiness-attributes.png)

This shows the environment is ready for automation scenarios like:

- Joiner / Mover / Leaver processes  
- Azure AD / Entra ID sync  
- Access reviews  

---

## Key Concepts Applied

- Role-Based Access Control (RBAC)  
- Least Privilege  
- Separation of Duties  
- OU-based delegation  
- Identity lifecycle management  
- Audit logging  

---

## What I Learned

This project helped me understand that delegation is not just about assigning permissions.

Some things that stood out:

- Permissions may appear to fail but still partially work, so validation is critical  
- Testing from the correct account (Helpdesk vs HR vs Admin) makes a big difference  
- The domain controller is always the source of truth for audit logs  
- Using groups instead of users simplifies everything later  
- Small mistakes in delegation can create unexpected behavior  

If I were to improve this, I would automate more of the validation and integrate it with scripts.

---

## Real-World Relevance

This is very similar to how companies manage Active Directory:

- Helpdesk handles daily support  
- HR manages user lifecycle  
- Managers validate access without modifying it  

This kind of delegation model is used in:

- IT support environments  
- enterprise identity management  
- compliance and audit processes  

---

## Skills Demonstrated

- Active Directory administration  
- PowerShell automation  
- RBAC design  
- Security delegation  
- Troubleshooting permissions  
- Audit validation  

---

## Technologies Used

- Windows Server 2022  
- Active Directory Domain Services  
- Windows 11  
- PowerShell  
- DSACLS  
- RSAT  
- Event Viewer  

---

