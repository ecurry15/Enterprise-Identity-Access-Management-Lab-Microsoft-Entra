# Enterprise Identity & Access Management Lab

<div align="center">
  <img src="https://github.com/user-attachments/assets/b7a5955e-33d3-4d89-aaeb-ca5298eb937a" width="900" alt="Lab Infrastructure Architecture" />
</div>

---

## Overview

This project demonstrates an enterprise-grade Microsoft Entra Identity and Access Management (IAM) environment built on Zero Trust principles and least privilege administration. The lab covers identity life-cycle management, PIM, conditional access enforcement, identity governance, automated audit reporting via PowerShell, and real-time security alerting using Log Analytics with KQL.

### Core Focus Areas

* **Identity Lifecycle:** Automated Joiner, Mover, and Leaver (JML) workflows.
* **Access Control:** Scoped RBAC using Dynamic Groups and Administrative Units.
* **Privileged Access:** Just-In-Time (JIT) role activation via PIM.
* **Zero Trust Security:** Enforced Conditional Access, IP fencing, and break-glass logging.
* **Governance & Detection:** Automated Access Reviews, PowerShell reporting, and KQL security alerts.
---

## Environment Architecture

### Administrative Units (AUs)
Administrative Units scope permissions to specific subsets of the organization. They are used to manage departments in this lab.

| Administrative Unit | Purpose |
| :--- | :--- |
| **HR** | Human Resources personnel |
| **Finance** | Finance personnel |
| **IT** | Information Technology personnel |
| **Sales** | Sales personnel |
| **Executives** | Executive leadership |
| **Marketing** | Marketing personnel |

---

### Security Groups
All security groups utilize **dynamic membership rules** to automate user assignment based on account properties.

| Group Name | Type | Membership Rule / Purpose |
| :--- | :--- | :--- |
| `SG-AllEmployees` | Dynamic | Baseline configuration (Licensing & conditional access) for active employees:<br>`(user.accountEnabled -eq true) and (user.department -ne "Contractors") and (user.department -ne "Interns")` |
| `SG-HR-Users` | Dynamic | `(user.department -eq "HR")` |
| `SG-Finance-Users` | Dynamic | `(user.department -eq "Finance")` |
| `SG-IT-Users` | Dynamic | `(user.department -eq "Information Technology")` |
| `SG-Executive-Users`| Dynamic | `(user.department -eq "Executives")` |
| `SG-Marketing-Users`| Dynamic | `(user.department -eq "Marketing")` |
| `SG-Contractors` | Dynamic | `(user.department -eq "Contractors")` |
| `SG-Interns` | Dynamic | `(user.department -eq "Interns")` |
| `SG-Disabled-Users` | Dynamic | Tracks deactivated accounts:<br>`(user.accountEnabled -eq false)` |

<div align="center">
  <img src="https://github.com/user-attachments/assets/4c5f6083-78ab-49f4-9df3-f0d9df3b88c9" width="850" alt="Dynamic Group Rule Configuration" />
  <br>
  <img src="https://github.com/user-attachments/assets/49ae8ab1-a7f7-4780-8c16-c5e3088b0825" width="850" alt="All Dynamic Security Groups Overview" />
</div>

---

### Role-Assignable Groups
To uphold least privilege, administrative roles are assigned directly to groups rather than individual user accounts. Users must request temporary membership to these groups via PIM.

| Role-Assignable Group | Entra ID Role | Eligible Accounts |
| :--- | :--- | :--- |
| `RAG-HelpdeskAdmins` | Helpdesk Administrator | HelpDeskAdmin |
| `RAG-UserAdmins` | User Administrator | IAMAdmin |
| `RAG-GroupAdmins` | Groups Administrator | IAMAdmin |
| `RAG-GlobalReaders` | Global Reader | SecOps |
| `RAG-HRhelpdesk` | Helpdesk Administrator (Scoped to HR AU) | IT Employees |

<div align="center">
  <img src="https://github.com/user-attachments/assets/134a7e5f-8491-4fbb-a9a4-d9cfca0342a5" width="850" alt="Role-Assignable Groups Overview" />
</div>

---

### Directory Structure

#### Administrative Accounts
* `IAMAdmin` (Eligible for User Admin, Groups Admin)
* `HelpDeskAdmin` (Eligible for Helpdesk Admin)
* `SecOps` (Eligible for Global Reader)

#### Standard User Accounts
* **HR:** Steph Curry, Draymond Green
* **Finance:** Chris Paul, Klay Thompson, Steve Kerr, Shawn Livingston
* **IT:** HelpDeskAdmin, IAMAdmin, SecOps, Andre Iggy, Jordan Poole
* **Executives:** Andrew Wiggins, Kevon Looney
* **Marketing:** Javale Mccgee, Harrison Barnes
* **Contractors:** Jimmy Butler, Kevin Durant
* **Interns:** Anthony Davis, LeBron James

<div align="center">
  <img src="https://github.com/user-attachments/assets/76015c31-d511-43c5-b035-89306b532856" width="850" alt="User Directory List" />
</div>

---

## Privileged Identity Management (PIM)

### Scenario 1: Just-In-Time Department Transfer
Intern LeBron James was hired as a full-time Marketing employee. `IAMAdmin` requires elevated rights to update LeBron's department attribute, which will automatically move him to the `SG-Marketing-Users` dynamic group.

1. **Pre-Activation:** `IAMAdmin` cannot access or modify user directory properties.
2. **PIM Activation:** `IAMAdmin` submits a justification request to activate the **User Administrator** role.
3. **Post-Activation:** `IAMAdmin` updates LeBron James's department attribute to `Marketing`.

<div align="center">
  <img src="https://github.com/user-attachments/assets/644c2447-23fa-4a7d-8f73-432884a98ef8" width="800" alt="IAMAdmin Blocked Before PIM" />
  <br>
  <img src="https://github.com/user-attachments/assets/1a4937ee-af91-4b67-972a-c59fe3c601da" width="800" alt="PIM Activation Request" />
  <br>
  <img src="https://github.com/user-attachments/assets/b6d74a85-8c47-4f48-bfac-36c5e78514cc" width="800" alt="User Property Updated Successfully" />
</div>

---

### Scenario 2: Scoped Administrative Unit Privileges
HR user Steph Curry submits a password reset ticket. IT analyst Andre Iggy activates his PIM role for `RAG-HRhelpdesk`. Because this role is scoped strictly to the **HR Administrative Unit**, Andre can reset Steph's password, but cannot reset passwords for users outside the HR unit.

1. **Pre-Activation:** Andre lacks permissions to reset passwords.
2. **PIM Activation:** Andre activates temporary access to the `RAG-HRhelpdesk` role.
3. **Post-Activation:** Password reset succeeds for Steph Curry (HR AU), but fails when attempted on a user outside the HR Administrative Unit.

<div align="center">
  <img src="https://github.com/user-attachments/assets/cd4ae221-daeb-42ff-9888-281e9e8fc772" width="800" alt="Reset Password Blocked" />
  <br>
  <img src="https://github.com/user-attachments/assets/c23e3f98-a079-43ed-a27b-98c765624fea" width="800" alt="PIM Role Activated for HR Group" />
  <br>
  <img src="https://github.com/user-attachments/assets/aa410ba4-f9a1-4814-9096-a22105b093fb" width="800" alt="Password Reset Success" />
  <br>
  <img src="https://github.com/user-attachments/assets/00f22d2f-6f37-4eba-b62f-399fb0fe7aa1" width="800" alt="Reset Password Blocked Outside AU" />
</div>

---

### Scenario 3: Incident Response Security Investigation
During a security incident investigation, analyst `SecOps` needs to review authentication methods and active roles for user Edward Campbell. `SecOps` activates temporary membership in the `RAG-GlobalReaders` group to obtain read-only directory visibility.

1. **Pre-Activation:** `SecOps` cannot inspect authentication details or roles.
2. **PIM Activation:** `SecOps` requests Global Reader activation.
3. **Post-Activation:** `SecOps` gains full visibility into authentication methods to conduct the security investigation.

<div align="center">
  <img src="https://github.com/user-attachments/assets/82d082b1-0a06-4c31-a48c-50300e04e2bd" width="800" alt="SecOps Access Denied" />
  <br>
  <img src="https://github.com/user-attachments/assets/932d20b0-18aa-407e-b258-b186cc3e71e0" width="800" alt="SecOps PIM Activation" />
  <br>
  <img width="800" alt="secOPs views AUth methods" src="https://github.com/user-attachments/assets/145b3c0e-c364-439f-9000-0910d7c1bbd8" />
</div>


---

## Identity Lifecycle Operations

### Joiner Process
When new employee **Shawn Livingston** is created with `Marketing` as his department attribute, dynamic membership rules automatically evaluate his profile, placing him into `SG-Marketing-Users` and `SG-AllEmployees`.

<div align="center">
  <img src="https://github.com/user-attachments/assets/17b73785-c947-48ad-90d9-a2f45e80b960" width="850" alt="New User Created and Auto-Assigned" />
</div>

---

### Mover Process
When **LeBron James** transitions from Intern to Marketing, updating his `Department` attribute to `Marketing` automatically revokes his `SG-Interns` group membership and assigns him to `SG-Marketing-Users`.

<div align="center">
  <img src="https://github.com/user-attachments/assets/f65f9714-eb38-49d8-9378-1ead7f28cc80" width="850" alt="LeBron Department Change" />
</div>

---

### Leaver Process
When **Harrison Barnes** is terminated, disabling his account (`accountEnabled = false`) and setting his department attribute to `Disabled` automatically removes him from `SG-AllEmployees` and shifts him into `SG-Disabled-Users`.

<div align="center">
  <img src="https://github.com/user-attachments/assets/9610e1b5-a5b5-40a0-82bc-5677ea19e6c7" width="850" alt="Disabled User Lifecycle State" />
</div>

---

## Conditional Access Policies

### 1. Mandatory MFA for All Users
Enforces Multi-Factor Authentication across all accounts accessing cloud apps.

<div align="center">
  <img src="https://github.com/user-attachments/assets/5c573031-a974-460a-88bc-a792909b7354" width="800" alt="MFA Policy Setup" />
  <br>
  <img src="https://github.com/user-attachments/assets/833bcebf-76d2-4ba2-8951-16f6a9385c96" width="800" alt="MFA User Assignment" />
  <br>
  <img src="https://github.com/user-attachments/assets/684fd109-62f1-4acf-bf60-eff3fc65abb8" width="800" alt="MFA Sign-In Log Success" />
</div>

---

### 2. Block Contractor Access to Admin Portals
Prevents external contractors (e.g., Jimmy Butler) from reaching admin management interfaces, including the Microsoft 365 Admin Center.

<div align="center">
  <img src="https://github.com/user-attachments/assets/80a068d2-9f7d-4f0a-8b91-a38ac1bb8e63" width="800" alt="Block Admin Portal Policy" />
  <br>
  <img src="https://github.com/user-attachments/assets/e62ae250-93e2-4889-a404-c949a5f39ac3" width="800" alt="Contractor Blocked Sign-In Screen" />
</div>

---

### 3. Location-Based Access Restriction (Interns)
Requires Intern accounts to sign in strictly from trusted corporate IP ranges ("Corporate Office" named location). Sign-ins originating outside these defined network boundaries are blocked.

<div align="center">
  <img src="https://github.com/user-attachments/assets/34af1290-2c2d-4e34-bf48-46410c59ace4" width="800" alt="Named Location CA Configuration" />
  <br>
  <img src="https://github.com/user-attachments/assets/e3e7c7fe-d212-4f9a-b2e7-7c0318fe2b63" width="800" alt="In-Office Sign-in Success" />
  <br>
  <img src="https://github.com/user-attachments/assets/2b1915a1-09f0-44a5-be73-9c19ecbf1ead" width="800" alt="Out-of-Office Sign-in Blocked" />
</div>

---

### Emergency Access ("Break Glass") Account
To protect against service lockouts or Conditional Access misconfigurations:
* A dedicated emergency account (`BreakGlass`) is excluded from all standard Conditional Access policies.
* Configured with strict sign-in alerts to notify SecOps via automated email immediately upon any login event.

---

## Log Analytics & KQL Security Alerts

### Log Forwarding Diagnostics
Entra ID `SignInLogs` are streamed to a central Log Analytics Workspace for long-term retention and security monitoring.

<div align="center">
  <img src="https://github.com/user-attachments/assets/abcc81c2-b309-439e-beef-6983503e10cf" width="850" alt="Diagnostic Settings Forwarding Logs" />
</div>

---

### Emergency Account KQL Detection Query
The following KQL query monitors for sign-in activity targeting the emergency break-glass account:

```kql
SigninLogs
| where UserId == "60afe846-8100-4aaf-a672-d5395308ca92"
| project TimeGenerated, UserPrincipalName, UserId, IPAddress
```

---

### Automated Alert Trigger
A test login on the `BreakGlass` account triggers an automated alert, dispatching an immediate notification email to the SOC team.

<div align="center">
  <img src="https://github.com/user-attachments/assets/ab0c5721-d378-46fd-ba82-6642fcbe8e73" width="800" alt="Azure Security Alert Triggered" />
  <br>
  <img src="https://github.com/user-attachments/assets/10a4b5e5-008d-4331-bb9e-f61080707c31" width="800" alt="Automated Email Notification Received" />
</div>

---

## Identity Governance & Access Reviews

### 1. Recurring Contractor Access Review Setup
To satisfy compliance requirements, a recurring monthly Access Review forces `IAMAdmin` to audit contractor group memberships.

<div align="center">
 <img width="850 "alt="Access review creation" src="https://github.com/user-attachments/assets/ae9ee87d-bd45-401c-a50b-93af44c0a9b8" />
</div>


---

### 2. Decision Execution & Deprovisioning Workflow
During the audit, `IAMAdmin` approves Jimmy Butler, but denies Kevin Durant. Because dynamic groups do not support automatic removal via access reviews, `IAMAdmin` manually disables Kevin Durant's account as part of the leaver workflow.

<div align="center">
  <img src="https://github.com/user-attachments/assets/7d265e34-8a4e-4f1f-aa90-2f0ae0a6ab6f" width="850" alt="Access Review Audit Results" />
  <br>
 
</div>

---

## PowerShell & Azure CloudShell Reporting

Automation scripts were executed in Azure CloudShell to audit user and group states.

### 1. disabled Users Audit
* **Export File:** [`deleted_azure_ad_users.json`](https://github.com/user-attachments/files/30730183/active_azure_ad_users.json)

```powershell
# Define output JSON path
$outputPath = "./deleted_azure_ad_users.json"
Write-Host "Fetching soft-deleted Azure AD users..." -ForegroundColor Cyan

# Query soft-deleted user objects
$deletedUsers = Get-AzADDeletedItem -Filter "isof('microsoft.graph.user')" | Select-Object `
    DisplayName, `
    UserPrincipalName, `
    Id, `
    DeletedDateTime, `
    UserType

# Export results to JSON
$deletedUsers | ConvertTo-Json -Depth 3 | Set-Content -Path $outputPath -Encoding UTF8
Write-Host "Successfully exported $($deletedUsers.Count) deleted users to $outputPath" -ForegroundColor Green
```

### 2. Disabled Users Audit
* **Export File:** [`disabled_azure_ad_users.json`](https://github.com/user-attachments/files/30730257/disabled_azure_ad_users.json)

```powershell
# Define output JSON path
$outputPath = "./disabled_azure_ad_users.json"
Write-Host "Fetching disabled Azure AD users..." -ForegroundColor Cyan

# Query disabled users (accountEnabled eq false)
$disabledUsers = Get-AzADUser -Filter "accountEnabled eq false" -Select "DisplayName","UserPrincipalName","Mail","UserType","AccountEnabled","Id","Department" | Select-Object `
    DisplayName, `
    UserPrincipalName, `
    Mail, `
    Department, `
    UserType, `
    AccountEnabled, `
    Id

# Export results to JSON
$disabledUsers | ConvertTo-Json -Depth 3 | Set-Content -Path $outputPath -Encoding UTF8
Write-Host "Successfully exported $($disabledUsers.Count) disabled users to $outputPath" -ForegroundColor Green
```
### 3. Group & Membership Hierarchy Audit
* **Export File:** [`azure_ad_groups_with_members.json`](https://github.com/user-attachments/files/30730357/azure_ad_groups_with_members.json)

```powershell
# Define output JSON path
$outputPath = "./azure_ad_groups_with_members.json"
Write-Host "Fetching Azure AD groups and members..." -ForegroundColor Cyan

# Fetch all groups
$groups = Get-AzADGroup
$groupList = [System.Collections.Generic.List[Object]]::new()

foreach ($group in $groups) {
    Write-Host "Processing group: $($group.DisplayName)" -ForegroundColor Gray  
    
    # Retrieve members for current group
    $members = Get-AzADGroupMember -GroupObjectId $group.Id | Select-Object `
        DisplayName, `
        UserPrincipalName, `
        UserType, `
        Id

    # Construct custom object
    $groupObject = [PSCustomObject]@{
        GroupName   = $group.DisplayName
        GroupId     = $group.Id
        Description = $group.Description
        MemberCount = ($members | Measure-Object).Count
        Members     = $members
    }
    $groupList.Add($groupObject)
}

# Export results to JSON
$groupList | ConvertTo-Json -Depth 4 | Set-Content -Path $outputPath -Encoding UTF8
Write-Host "Successfully exported $($groupList.Count) groups and their members to $outputPath" -ForegroundColor Green
```
---

## Challenges & Lessons Learned

### 1. Role-Assignable Groups Scoped to Administrative Units
* **Challenge:** I planned to use PIM for groups scoped to an Administrative Unit (AU) so users could gain temporary AU-level permissions. However, assigning the role to the group granted directory-wide access instead of restricting it to the AU.
* **Key Insight:** PIM for groups applies permissions tenant-wide across the directory, overriding the local AU scope.
* **Resolution:** Configured PIM eligibility directly on individual user accounts for the AU-scoped role rather than through the group.

---

### 2. Access Reviews on Dynamic Security Groups
* **Challenge:** Access reviews could not automatically remove denied users from dynamically assigned security groups.
* **Key Insight:** Access reviews do not modify user profile attributes (e.g., Department), which are what drive dynamic group membership rules.
* **Resolution:** Handled user removal manually as part of the leaver workflow for this lab. Future setups will use static groups to take full advantage of automated deprovisioning.

---

### 3. Azure Log Analytics Workspace Access
* **Challenge:** My Global Admin account could not access or manage the Log Analytics Workspace.
* **Key Insight:** Global Admin grants control over the Microsoft Entra **Identity plane**, but does not grant rights to Azure resources in the **Resource plane** (Azure Subscriptions).
* **Resolution:** Navigated to the Azure Subscription IAM settings and assigned the **Log Analytics Contributor** Azure RBAC role to the account.




---

## Summary

This lab demonstrates secure, production-ready Microsoft Entra ID administration. Key achievements:

1. **Automated Lifecycle Management:** Attribute-driven dynamic security groups eliminate manual group maintenance during Joiner, Mover, and Leaver events.
2. **Strict Least Privilege:** Implementing Role-Assignable Groups scoped to Administrative Units guarantees admins only possess permissions required for their assigned department.
3. **Zero Trust Access Controls:** Conditional Access policies enforce MFA universally, restrict portal access for contractors, and fence intern authentication to corporate IP ranges.
4. **Proactive SOC Monitoring:** Streaming Entra ID diagnostic logs to a Log Analytics Workspace enables automated KQL alert rules that immediately notify security analysts when critical accounts (such as Break Glass emergency admins) authenticate.
