# Enterprise Identity & Access Management Lab
Microsoft Entra | PIM | RBAC | Conditional Access | Identity Governance

<img src="blob:chrome-untrusted://media-app/2a3ecc02-dddf-4764-af65-3a03187810b5" alt="IAM lab Architecture.png"/>

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

---

## Security Groups

| Group | Purpose |
|-------|---------|
| SG-AllEmployees | Baseline access for all employees |
| SG-HR-Users | Human Resources employees |
| SG-Finance-Users | Finance employees |
| SG-IT-Users | IT employees |
| SG-Contractors | Contractor accounts |

## Role-Assignable Groups

| Role-Assignable Group | Entra Role | Purpose |
|-----------------------|------------|---------|
| RAG-HelpdeskAdmins | Helpdesk Administrator | Password resets and account unlocks |
| RAG-UserAdmins | User Administrator | User lifecycle management |
| RAG-GroupAdmins | Groups Administrator | Security and Microsoft 365 group administration |
| RAG-GlobalReaders | Global Reader | Read-only access for security investigations |

---

## Administrative Accounts

| Account | Purpose | PIM Eligible Roles |
|----------|---------|--------------------|
| IAMAdmin01 | Identity Administrator | User Administrator, Groups Administrator |
| Helpdesk01 | Tier 1 Help Desk | Helpdesk Administrator |
| SecOps01 | Security Operations | Global Reader |

---

## Standard Users

### Human Resources

- HR-EMP01
- HR-EMP02

### Finance

- FIN-EMP01
- FIN-EMP02

### Information Technology

- IT-EMP01
- IT-EMP02

### Contractors

- CONT-EMP01
- CONT-EMP02

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
