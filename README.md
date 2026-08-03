# Enterprise Identity & Access Management Lab

<img width="1336" height="824" alt="IAM lab Architecture" src="https://github.com/user-attachments/assets/0c16eb59-3a73-4056-8416-2cd8a37f4ca7" />

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

- Lebron James
- Javale Mccgee

### Contractors

- Jimmy Butler

### Interns

- Anthony Davis

### Disabled

- Kevin Durant
- Harrison Barnes

<img width="1620" height="580" alt="users" src="https://github.com/user-attachments/assets/76015c31-d511-43c5-b035-89306b532856" />

---
# Privileged Identity Management (PIM)

### PIM Eligibility Created 

## PIM Executed Scenarios

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

#### New employee Shawn Livingston is created.



- User is added to the Marketing and AllEmployees security groups by their department and "AccountEnabled" attributes.


<img width="1620" height="574" alt="Shawn L user created" src="https://github.com/user-attachments/assets/17b73785-c947-48ad-90d9-a2f45e80b960" />

---

# Mover Process

#### Intern Lebron James is moved to the Marketing Department.

- LeBron's department attribute is changed from "interns" to "Marketing"

<img width="1620" height="571" alt="Lebron from intern to marketing" src="https://github.com/user-attachments/assets/f65f9714-eb38-49d8-9378-1ead7f28cc80" />

---

# Leaver Process

#### Employee Harrison Barnes is terminated, and their account is disabled.

- The "Account enabled" attribute is unchecked, and the user's department is changed to "disabled". This removes them from their original department, removes them from the AllEmployees group, and adds them to the Disabled-users group.

  <img width="1620" height="582" alt="H-barns disabled" src="https://github.com/user-attachments/assets/9610e1b5-a5b5-40a0-82bc-5677ea19e6c7" />


---

# Conditional Access

## MFA Required for ALL Users

- [ ] MFA required
- [ ] Access granted

### Evidence
- 📷 Sign-in Log
- 📷 Conditional Access Result

---

## Contractors are blocked from Admin Portals 

- [ ] MFA required
- [ ] Admin portal blocked

### Evidence
- 📷 Blocked Sign-in
- 📷 Conditional Access Result

---

## Interns must sign in inside the Corporate Office

- [ ] MFA required
- [ ] Admin portal blocked

### Evidence
- 📷 Blocked Sign-in
- 📷 Conditional Access Result

---

## Break Glass

- [ ] Excluded from most MFA policies but must use a different MFA method 

### Evidence
- 📷 Successful Sign-in

---

# Security Validation

| Test | Expected | Status |
|------|----------|:------:|
| HR password reset | Success | ☐ |
| Finance password reset | Denied | ☐ |
| PIM activation | Success | ☐ |
| PIM expiration | Access removed | ☐ |
| Disabled account login | Blocked | ☐ |
| Employee MFA | Success | ☐ |
| Contractor admin portal | Blocked | ☐ |
| Access Review removal | Success | ☐ |

### Evidence
- 📷 Test Results

---

# Identity Governance

## Access Reviews

- [ ] Review created
- [ ] Reviewer assigned
- [ ] Approval tested
- [ ] Denial tested
- [ ] Group membership updated

### Evidence
- 📷 Access Review
- 📷 Audit Log

---

# Authentication Methods

Verify user authentication methods.

- [ ] Microsoft Authenticator
- [ ] Phone
- [ ] Email (if configured)

### Evidence
- 📷 Authentication Methods

---

# Audit Logs

Verify logs exist for:

- [ ] User Created
- [ ] User Updated
- [ ] Group Membership Changed
- [ ] PIM Activated
- [ ] Password Reset
- [ ] User Disabled

### Evidence
- 📷 Audit Logs

---

# Sign-in Logs

Verify:

- [ ] Successful sign-in
- [ ] MFA sign-in
- [ ] Blocked sign-in
- [ ] PIM activation
- [ ] Break Glass sign-in

### Evidence
- 📷 Sign-in Logs

---

# PowerShell Validation

Run and verify:

- [ ] Export Users
- [ ] Export Groups
- [ ] Export Role Assignments
- [ ] Export PIM Assignments
- [ ] Export Disabled Users
- [ ] Export Licenses

### Evidence
- 📷 PowerShell Output
- 📷 CSV Files

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

---
### Emergency Accounts

- BreakGlass01
