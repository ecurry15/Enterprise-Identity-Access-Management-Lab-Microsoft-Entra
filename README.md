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


1. All Security groups are dynamic user groups. Users are added to their respective group based on their department attribute
2. The "AllEmployees" group allows us to implement baseline configurations for all active employees like licenses, conditional access, etc. Users  are added to this group using this query `(user.accountEnabled -eq true) and (user.department -ne "Contractors") and (user.department -ne "Interns")`.
3. The "Disabled-users" group allows us to track disabled accounts. Users are added to this group using this query `(user.accountEnabled -eq false)`. 

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

1. These assigned membership groups are used to implement least privilege. Roles are applied to the group rather than to an individual user. When a user needs access to role permissions, eligible users can request to join the group through PIM and receive temporary role permissions within the group.

<img width="1266" height="554" alt="Role groups" src="https://github.com/user-attachments/assets/134a7e5f-8491-4fbb-a9a4-d9cfca0342a5" />


---

## Administrative Accounts

| Account | Purpose | PIM Eligible Roles |
|----------|---------|--------------------|
| IAMAdmin | Identity Administrator | User Administrator, Groups Administrator |
| Helpdesk | Tier 1 Help Desk | Helpdesk Administrator |
| SecOps | Security Operations Employee | Global Reader |

---

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
---
# Privileged Identity Management (PIM)

For each role:

- [ ] Verify role is **not active**
- [ ] Activate role
- [ ] MFA prompted
- [ ] Justification entered
- [ ] Administrative task completed
- [ ] Deactivate / Expire role
- [ ] Verify access removed

### Evidence
- 📷 PIM Activation
- 📷 Active Role
- 📷 PIM Audit Log

---

# Joiner Process

New employee created.

Verify:

- [ ] User created
- [ ] Department assigned
- [ ] Group memberships
- [ ] MFA policy applied
- [ ] Application access verified

### Evidence
- 📷 New User
- 📷 Group Memberships
- 📷 Audit Log

---

# Mover Process

Moved employee to another department.

Verify:

- [ ] Department updated
- [ ] Previous groups removed
- [ ] New groups assigned
- [ ] Conditional Access still applies
- [ ] Application access updated

### Evidence
- 📷 Before
- 📷 After
- 📷 Audit Log

---

# Leaver Process

Disabled employee account.

Verify:

- [ ] Account disabled
- [ ] Sessions revoked
- [ ] Licenses removed
- [ ] Groups removed
- [ ] PIM removed
- [ ] Sign-in blocked

### Evidence
- 📷 Disabled Account
- 📷 Audit Log
- 📷 Failed Sign-in

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
