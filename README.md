# Enterprise Identity & Access Management Lab

<img width="1336" height="824" alt="Lab image final" src="https://github.com/user-attachments/assets/b7a5955e-33d3-4d89-aaeb-ca5298eb937a" />



# Overview

## Focus:

- Identity Lifecycle Management (Joiner, Mover, Leaver)
- Role-Based Access Control (RBAC)
- Privileged Identity Management (PIM)
- Administrative Units
- Conditional Access
- Identity Governance
- Least Privilege Administration
- Zero Trust Principles
- CloudShell/Powershell automation
- Log Analytics Workspace KQL Alerts

---

# Environment

## Administrative Units

| Administrative Unit | Purpose |
|---------------------|---------|
| HR | Human Resources users |
| Finance | Finance users |
| IT | Information Technology users |
| Sales | Sales users |
| Executives | Executives |
| Marketing | Marketing users |


---

## Security Groups

| Group | Purpose |
|-------|---------|
| SG-AllEmployees | Baseline access for all employees |
| SG-HR-Users | Human Resources employees |
| SG-Finance-Users | Finance employees |
| SG-IT-Users | IT employees |
| SG-Executive-Users | Executive employees 
| SG-Marketing-Users | Marketing employees |
| SG-Contractors | Contractor accounts |
| SG-Interns | Intern accounts |
| SG-Disabled-Users | De-activated user accounts |


- All Security groups are dynamic user groups. Users are added to their respective group based on their department attribute
- The "AllEmployees" group allows us to implement baseline configurations for all active employees like licenses, conditional access, etc. Users  are added to this group using this query `(user.accountEnabled -eq true) and (user.department -ne "Contractors") and (user.department -ne "Interns")`.
- The "Disabled-users" group allows us to track disabled accounts. Users are added to this group using this query `(user.accountEnabled -eq false)`. 

<img width="1520" height="677" alt="dynamic group query" src="https://github.com/user-attachments/assets/4c5f6083-78ab-49f4-9df3-f0d9df3b88c9" />
<img width="1366" height="577" alt="All dynamic groups" src="https://github.com/user-attachments/assets/49ae8ab1-a7f7-4780-8c16-c5e3088b0825" />

---


## Role-Assignable Groups

| Role-Assignable Group | Entra Role | Eligable Users |
|-----------------------|------------|---------|
| RAG-HelpdeskAdmins | Helpdesk Administrator | HelpDeskAdmin |
| RAG-UserAdmins | User Administrator | IAMAdmin |
| RAG-GroupAdmins | Groups Administrator | IAMAdmin |
| RAG-GlobalReaders | Global Reader | SecOps |
| RAG-HRhelpdesk | Helpdesk Administrator | IT employees

- These assigned membership groups are used to implement least privilege. Roles are applied to the group rather than to an individual user. When a user needs access to role permissions, eligible users can request to join the group through PIM and receive temporary role permissions within the group.

<img width="1266" height="554" alt="Role groups" src="https://github.com/user-attachments/assets/134a7e5f-8491-4fbb-a9a4-d9cfca0342a5" />


---

## Administrative Accounts

| Account | Purpose | PIM Eligible Roles |
|----------|---------|--------------------|
| IAMAdmin | Identity Administrator | User Administrator, Groups Administrator |
| Helpdesk | Tier 1 Help Desk | Helpdesk Administrator |
| SecOps | Security Operations Employee | Global Reader |


## Standard Users

### Human Resources

- Steph Curry
- Draymond Green

### Finance

- Chris Paul
- Klay Thompson
- Steve Kerr
- Shawn Livingston

### Information Technology

- HelpDeskAdmin
- IAMAdmin
- SecOps
- Andre Iggy
- Jordan Poole

### Executives

- Andrew Wiggins
- Kevon Looney

### Marketing

- Javale Mccgee
- Harrison Barnes

### Contractors

- Jimmy Butler
- Kevin Durant

### Interns

- Anthony Davis
- Lebron James

<img width="1620" height="580" alt="users" src="https://github.com/user-attachments/assets/76015c31-d511-43c5-b035-89306b532856" />

---
# Privileged Identity Management (PIM)

## PIM Utilized Scenarios

### Scenario 1
Intern Lebron James has been hired as a full-time marketing employee. The employee needs to be moved from the Interns security group to the Marketing security group. IAMAdmin activates their eligible PIM membership to the UserAdmins group. With their new temporary role permissions, they change Lebron James's department attribute to Marketing, which moves the user to the Marketing group.

#### Before PIM is Activated  
- IAMAdmin cannot view user properties.
<img width="1366" height="568" alt="IAMAdmin cannot see user properties before PIM" src="https://github.com/user-attachments/assets/644c2447-23fa-4a7d-8f73-432884a98ef8" />

#### PIM Activation  
<img width="1366" height="579" alt="IAMadmin activates PIM reason" src="https://github.com/user-attachments/assets/1a4937ee-af91-4b67-972a-c59fe3c601da" />

#### After PIM is Activated
- IAMAdmin can now edit user properties.
<img width="1366" height="583" alt="Lebron Marketing" src="https://github.com/user-attachments/assets/b6d74a85-8c47-4f48-bfac-36c5e78514cc" />

---

### Scenario 2
HR employee Steph Curry creates a ticket with IT to get their password reset. IT user Andre Iggy takes the ticket and enables his PIM membership to the HRHelpdesk group. This group has helpdesk admin privileges scoped to the HelpDesk administrative unit. Andre Iggy then resets Steph Curry's password.

#### Before PIM is Activated
- Andre does not have permission to reset passwords
<img width="1366" height="580" alt="Iggy can&#39;t reset PW before activating pim" src="https://github.com/user-attachments/assets/cd4ae221-daeb-42ff-9888-281e9e8fc772" />

#### PIM Activation
<img width="1752" height="861" alt="Andre-Iggy PIM activated" src="https://github.com/user-attachments/assets/c23e3f98-a079-43ed-a27b-98c765624fea" />

#### After PIM is Activated
- Andre successfully resets Steph's password
<img width="1366" height="581" alt="Andre Iggy resets steph pw" src="https://github.com/user-attachments/assets/aa410ba4-f9a1-4814-9096-a22105b093fb" />

---

#### Andre cannot reset users' passwords outside of the HR Administrative Unit
 <img width="1366" height="574" alt="Andre Iggy can&#39;t reset PW outside AU" src="https://github.com/user-attachments/assets/00f22d2f-6f37-4eba-b62f-399fb0fe7aa1" />

### Scenario 3
A user account needs to be analyzed to investigate a security incident. The SecOps employee activates their eligible PIM assignment, gaining temporary access to the GlobalReaders group. With the global reader role, they can view the user Edward Campbell's authentication methods and assigned roles.

#### Before PIM is Activated
- SecOps does not have permission to view Edward's role assignments.
<img width="1366" height="578" alt="secOps cannot see AUth method" src="https://github.com/user-attachments/assets/82d082b1-0a06-4c31-a48c-50300e04e2bd" />

#### PIM Activation
<img width="1366" height="564" alt="secOPs activates PIM" src="https://github.com/user-attachments/assets/932d20b0-18aa-407e-b258-b186cc3e71e0" />


#### After PIM is Activated
- SecOps views authentication methods
<img width="1366" height="581" alt="secOPs views AUth methods" src="https://github.com/user-attachments/assets/4846461f-2732-4b56-935c-3efa078babb5" />

---

# Joiner Process

### New employee Shawn Livingston is created.



- User is added to the Marketing and AllEmployees security groups by their department and "AccountEnabled" attributes.


<img width="1620" height="574" alt="Shawn L user created" src="https://github.com/user-attachments/assets/17b73785-c947-48ad-90d9-a2f45e80b960" />

---

# Mover Process

### Intern LeBron James is moved to the Marketing Department.

- LeBron's department attribute is changed from "interns" to "Marketing"

<img width="1620" height="571" alt="Lebron from intern to marketing" src="https://github.com/user-attachments/assets/f65f9714-eb38-49d8-9378-1ead7f28cc80" />

---

# Leaver Process

### Employee Harrison Barnes is terminated, and their account is disabled.

- The "Account enabled" attribute is unchecked, and the user's department is changed to "disabled". This removes them from their original department, removes them from the AllEmployees group, and adds them to the Disabled-users group.

  <img width="1620" height="582" alt="H-barns disabled" src="https://github.com/user-attachments/assets/9610e1b5-a5b5-40a0-82bc-5677ea19e6c7" />


---

# Conditional Access

### MFA Required for ALL Users

- MFA is required for every account

<img width="1341" height="576" alt="MFA required policy" src="https://github.com/user-attachments/assets/5c573031-a974-460a-88bc-a792909b7354" />  
<img width="1366" height="571" alt="MFA required all users" src="https://github.com/user-attachments/assets/833bcebf-76d2-4ba2-8951-16f6a9385c96" />
<img width="1349" height="565" alt="MFA required success log" src="https://github.com/user-attachments/assets/684fd109-62f1-4acf-bf60-eff3fc65abb8" />



---

### Contractors are blocked from Admin Portals 

- The policy successfully blocks contractor Jimmy Butler's access to the 365 Admin center.

<img width="1620" height="568" alt="block admin portal contractors CA" src="https://github.com/user-attachments/assets/80a068d2-9f7d-4f0a-8b91-a38ac1bb8e63" />

<img width="1366" height="596" alt="JImmy contractor cannot log into admin portals" src="https://github.com/user-attachments/assets/e62ae250-93e2-4889-a404-c949a5f39ac3" />


---

### Interns must sign in inside the Corporate Office

- A named location "Corporate Office" is created with my personal IPV4 and IPV6 addresses. The CA policy is then created to block all sign-ins excluding sign-ins from the named location.
- The policy is set to read-only for testing
- The policy successfully allows a sign-in from inside the named location and blocks a sign-in from outside the named location.

### Policy Creation
<img width="1920" height="865" alt="Interns CA Policy creation" src="https://github.com/user-attachments/assets/34af1290-2c2d-4e34-bf48-46410c59ace4" /> 

### Successful In-office sign-in
<img width="1665" height="860" alt="Intern-In-Office-log-in" src="https://github.com/user-attachments/assets/e3e7c7fe-d212-4f9a-b2e7-7c0318fe2b63" />  

### Successful block Out-of-office sign-in
<img width="1621" height="891" alt="Intern sign-in outside office" src="https://github.com/user-attachments/assets/2b1915a1-09f0-44a5-be73-9c19ecbf1ead" />

---

### Break Glass

- The BreakGlass account is excluded from ALL conditional access policies.
- Microsoft requires MFA, and in a real-world environment, this account would use a physical security key like FIDO2.
- However, since I don't have a FIDO2 key and sms is being retired, this account is still required to use the authenticator app.
- This account also has a sign-in alert configured for every successful sign-in.

---

# Log Analytics Alerts / KQL

### Diagnostic Settings / Log forwarding created  

- Forwards SignInLogs to Log Analytics Workspace
<img width="1405" height="568" alt="log forwarding" src="https://github.com/user-attachments/assets/abcc81c2-b309-439e-beef-6983503e10cf" />

### Sign-in Alert created using KQL 

- KQL Query Used to find the Event

  `` SigninLogs
  | where UserId == "60afe846-8100-4aaf-a672-d5395308ca92"
  | project TimeGenerated, UserPrincipalName, UserId, IPAddress ``

  <img width="1428" height="568" alt="KQL query" src="https://github.com/user-attachments/assets/eac1d2e2-d53c-43fb-9961-814c9cc27fb1" />


### Alert successfully triggered

- The Breakglass account signs in and the alert is triggered. Email alerts are also configured.

<img width="1380" height="534" alt="Break glass alert triggered" src="https://github.com/user-attachments/assets/ab0c5721-d378-46fd-ba82-6642fcbe8e73" />

<img width="1527" height="521" alt="alert email" src="https://github.com/user-attachments/assets/10a4b5e5-008d-4331-bb9e-f61080707c31" />


---

# Identity Governance

## Access Review

### Creation: Review Contractors Monthly

- IAMAdmin is added as a reviewer to confirm if the contractor users are still active. They are to approve or deny users' membership in the contractor group monthly.

<img width="1620" height="780" alt="Access review creation" src="https://github.com/user-attachments/assets/2939c946-1ed4-4d1a-a4e7-629b507de43e" />



### IAMAdmin completes Access Review

- IAMAdmin has approved Jimmy Butler but denied Kevin Durant.

<img width="1425" height="501" alt="Access review completed" src="https://github.com/user-attachments/assets/7d265e34-8a4e-4f1f-aa90-2f0ae0a6ab6f" />

### Leaver Process 

- Due to the contractor group being a dynamic security group, automatic removal of users is not allowed. Therefore, "Auto apply results to resource" is disabled.
- IAMAdmin completes the review, then manually disables Kevin Durant's account.

---

# PowerShell/Cloudshell Validation

### Exported Active Users  

- File: [active_azure_ad_users.json](https://github.com/user-attachments/files/30730183/active_azure_ad_users.json)

- Script Ran

```
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

### Exported Disabled Users  

- File: [disabled_azure_ad_users.json](https://github.com/user-attachments/files/30730257/disabled_azure_ad_users.json)

- Script Ran

```
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

### Exported Groups 

- File: [azure_ad_groups_with_members.json](https://github.com/user-attachments/files/30730357/azure_ad_groups_with_members.json)

- Script Ran

```
# Define output JSON path
$outputPath = "./azure_ad_groups_with_members.json"
Write-Host "Fetching Azure AD groups and members..." -ForegroundColor Cyan
# Fetch all groups
$groups = Get-AzADGroup
$groupList = [System.Collections.Generic.List[Object]]::new()
foreach ($group in $groups) {
    Write-Host "Processing group: $($group.DisplayName)" -ForegroundColor Gray  
    # Retrieve members for the current group
    $members = Get-AzADGroupMember -GroupObjectId $group.Id | Select-Object `
        DisplayName, `
        UserPrincipalName, `
        UserType, `
        Id
    # Construct custom group object
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

# Final Validation Summary

| Category | Complete |
|----------|:--------:|
| Environment | ☐ |
| RBAC | ☐ |
| Administrative Units | ☐ |
| PIM | ☐ |
| Joiner | ☐ |
| Mover | ☐ |
| Leaver | ☐ |
| Conditional Access | ☐ |
| Identity Governance | ☐ |
| Authentication | ☐ |
| Audit Logs | ☐ |
| Sign-in Logs | ☐ |
| PowerShell | ☐ |
| Security Validation | ☐ |
| Documentation | ☐ |

