# Microsoft Entra ID Joiner–Mover–Leaver Lifecycle Lab

**Author:** Aaron Smith  
**Completed:** September 25, 2026  
**Environment:** Microsoft Entra ID Premium P2 developer sandbox  
**Project type:** Independent simulated IAM portfolio project

> This project was completed in a personal Microsoft Entra ID developer sandbox. It demonstrates hands-on IAM administration and decision-making but does not represent paid production IAM experience.

## Executive Summary

I designed and executed a simulated Joiner–Mover–Leaver (JML) identity lifecycle for Northstar Community Services, a fictional 250-person organization. The lab covered employee onboarding, a departmental transfer, employee offboarding, and a time-limited external auditor engagement.

I used Microsoft Entra ID security groups to model birthright and role-based access, updated identity attributes and manager relationships, removed obsolete access, disabled departing identities, revoked active sessions, invited and redeemed a B2B guest account, and reviewed Entra audit logs to validate the work.

## Scenario

Northstar Community Services uses Microsoft Entra ID as its central identity platform. Human Resources confirms employment events, managers confirm business need, data or application owners authorize sensitive access, and IAM performs and validates the technical changes.

Four lifecycle events were simulated:

1. **Joiner — Maya Chen:** New Accounts Payable Specialist joining Finance.
2. **Mover — Jordan Ellis:** Sales Operations Coordinator transferring to HR Operations.
3. **Leaver — Devon Price:** Operations Analyst whose employment ended at a defined effective time.
4. **External guest — Priya Nair:** Finance auditor receiving restricted access for a 30-day engagement.

## Business Problem

Organizations must provide workers with timely access while preventing excessive, stale, or unauthorized permissions. Poor lifecycle management can cause:

- Productivity delays during onboarding
- Access creep during departmental transfers
- Former employees retaining access
- External guests retaining access after engagements end
- Weak separation of duties and undocumented approvals
- Audit findings caused by incomplete evidence

## IAM Principles and Controls

| Principle or control | Application in this lab |
|---|---|
| Least privilege | Each identity received only the groups required for its role or engagement. |
| Role-based access control | Security groups represented standardized employee, departmental, and job-function access. |
| Group-based access | Access was modeled through groups instead of individual direct assignments. |
| Separation of duties | HR or a sponsor initiated events, managers and owners approved access, and IAM executed the changes. |
| Access recertification | A mover's old access was explicitly evaluated and removed rather than only adding new permissions. |
| Prompt deprovisioning | Leaver and expired-guest identities were disabled, sessions revoked, and entitlements removed. |
| Auditability | Screenshots and Entra audit events were retained as evidence of configuration and validation. |

## Responsibility Model

| Event | Initiates or confirms | Approves business access | Performs change | Validates result |
|---|---|---|---|---|
| Employee joins | HR | Manager and resource owner | IAM Analyst | IAM and manager/resource owner |
| Employee moves | HR and new manager | New manager and resource owner | IAM Analyst | IAM and new manager/resource owner |
| Employee leaves | HR | HR authorizes event and timing | IAM Analyst | IAM with completion confirmation to HR |
| Guest engagement | Internal sponsor | Sponsor and resource owner | IAM Analyst | IAM and sponsor/resource owner |

## Access Model

I created seven assigned-membership security groups:

| Group | Purpose |
|---|---|
| `LAB-NSCS-All-Employees` | Birthright access for active employees |
| `LAB-NSCS-Finance` | General Finance department access |
| `LAB-NSCS-Finance-AP` | Accounts Payable role access |
| `LAB-NSCS-Sales-Ops` | Sales Operations role access |
| `LAB-NSCS-HR-Ops` | HR Operations role access |
| `LAB-NSCS-Operations` | General Operations access |
| `LAB-NSCS-Finance-Audit-Guests` | Restricted access for approved external Finance auditors |

These groups represented access entitlements for the simulation. No production applications or production data were used.

## Lifecycle Execution

### 1. Joiner: Maya Chen

**Business requirement:** Provision a new Accounts Payable Specialist with standard employee, Finance, and Accounts Payable access.

**Starting state:** Maya's identity was prestaged with the account disabled and no lab group memberships.

**Actions performed:**

- Created the identity as an internal Member user.
- Recorded job title, department, employee ID, hire date, and manager.
- Enabled the account at the simulated start date.
- Added Maya to:
  - `LAB-NSCS-All-Employees`
  - `LAB-NSCS-Finance`
  - `LAB-NSCS-Finance-AP`
- Confirmed that no administrator role was assigned.

**Validation:** Verified that the account was enabled, Lena Brooks was recorded as manager, and Maya had the three intended lab group memberships.

### 2. Mover: Jordan Ellis

**Business requirement:** Transfer Jordan from Sales Operations to HR Operations without leaving stale Sales access.

**Starting access:**

- `LAB-NSCS-All-Employees`
- `LAB-NSCS-Sales-Ops`
- A pre-existing tenant Microsoft 365 group named `MSFT`

**Actions performed:**

- Updated Jordan's department to Human Resources.
- Updated the job title to HR Operations Analyst.
- Changed the manager to Avery Patel.
- Removed `LAB-NSCS-Sales-Ops`.
- Added `LAB-NSCS-HR-Ops`.
- Retained employee birthright access and the pre-existing tenant group.

**Validation:** Verified that Jordan retained employee access, received HR Operations access, and no longer had the Sales Operations group. Reviewed successful Entra audit events for user and group-management activity.

### 3. Leaver: Devon Price

**Business requirement:** Terminate access at the confirmed departure time while retaining the identity record for audit and retention purposes.

**Starting access:**

- `LAB-NSCS-All-Employees`
- `LAB-NSCS-Operations`
- Pre-existing `MSFT` tenant group

**Actions performed:**

- Recorded the employee leave date.
- Disabled the account.
- Revoked active sessions.
- Removed all three group memberships.
- Verified that the user had no applications, assigned roles, or licenses.
- Retained the disabled identity instead of deleting it immediately.

**Validation:** Entra showed the account as disabled with zero group memberships, applications, roles, and licenses. Audit logs showed successful account disablement, session-token revocation, and membership removals.

### 4. External Guest: Priya Nair

**Business requirement:** Provide a Finance auditor with restricted, sponsor-owned access for a 30-day engagement.

**Actions performed:**

- Invited an external identity as a Guest user.
- Recorded the auditor role, company, internal sponsor, and engagement end date.
- Assigned only `LAB-NSCS-Finance-Audit-Guests`.
- Completed B2B invitation redemption using email one-time passcode authentication.
- Confirmed the invitation state was Accepted.
- Confirmed that no license, application, or directory role was assigned.

**Troubleshooting:** The original invitation email was not received. I inspected the guest's B2B invitation state, resent the invitation, used the returned redemption link, and successfully completed redemption with a one-time passcode delivered to the external email account.

#### Time-Compressed Expiration Simulation

On September 25, 2026, I performed a time-compressed simulation of the guest engagement expiration scheduled for October 25, 2026. Acting on a simulated sponsor confirmation that no extension was required, I disabled the guest identity, revoked active sessions, removed the Finance audit group membership, and retained the identity object for audit and possible future re-engagement.

**Validation:** Priya remained an Accepted external Guest, but the account was disabled and had zero group memberships, applications, roles, and licenses.

## Before-and-After Access Summary

| Identity/event | Before | After | Control demonstrated |
|---|---|---|---|
| Maya — Joiner | Disabled; no lab groups | Enabled; Employee + Finance + AP groups | Controlled provisioning and role-based access |
| Jordan — Mover | Employee + Sales Operations | Employee + HR Operations | Removal of stale access and prevention of access creep |
| Devon — Leaver | Employee + Operations + tenant baseline | Disabled; sessions revoked; zero groups | Prompt deprovisioning and identity retention |
| Priya — Guest expiration | Enabled Guest; restricted audit group | Disabled Guest; sessions revoked; zero groups | Time-bound external access and sponsor accountability |

## Evidence

Before publishing screenshots, redact personal email addresses, tenant IDs, object IDs, passwords, and any other identifying tenant information. Use solid opaque boxes rather than translucent highlighting.

Recommended evidence filenames:

| File | Evidence shown |
|---|---|
| `01-security-groups-created.png` | Seven `LAB-NSCS` security groups |
| `02-jordan-before-move.png` | Jordan's Employee and Sales Operations access |
| `03-maya-joiner-profile.png` | Maya's role, department, and manager |
| `04-maya-joiner-groups.png` | Maya's three approved groups |
| `05-jordan-after-move.png` | HR Operations access with Sales access removed |
| `06-jordan-audit-log.png` | Successful mover-related audit events |
| `07-devon-before-leaver.png` | Devon's pre-offboarding access |
| `08-devon-disabled.png` | Disabled account and zero assigned access |
| `09-devon-audit-log.png` | Disable, token revocation, and group removals |
| `10-priya-guest-accepted.png` | Accepted B2B guest state and restricted access |
| `11-priya-guest-disabled.png` | Disabled guest with zero assigned access |
| `12-priya-audit-log.png` | Guest expiration actions |

After adding the files to the `evidence` folder, selected screenshots can be embedded like this:

```markdown
![Maya's approved Joiner group memberships](evidence/04-maya-joiner-groups.png)

![Devon's successful offboarding audit events](evidence/09-devon-audit-log.png)
```

## Findings

1. Group-based access made lifecycle changes clearer and easier to verify than scattered direct assignments.
2. A mover event must remove obsolete access; adding new access alone creates access creep.
3. Disabling an account and revoking sessions are distinct controls. Both were used during offboarding.
4. Guest identity ownership belongs to the internal sponsor and resource owner, not solely to IAM.
5. A guest object can be retained for audit or re-engagement while its access remains removed and the account disabled.
6. Audit logs provide technical evidence, but authorization records and business approvals would also be required in production.

## Recommendations for Production

- Integrate an authoritative HR system to trigger Joiner, Mover, and Leaver events.
- Use dynamic groups or automated provisioning where attributes and licensing support them.
- Map security groups to real enterprise applications, SharePoint sites, and data resources.
- Require resource-owner approval for sensitive access.
- Use entitlement management or another governed workflow for guest access with enforced expiration.
- Stage and test high-impact changes before enforcement.
- Establish documented retention periods for disabled users and guests.
- Monitor failed lifecycle operations and require independent review for privileged access changes.

## Limitations

- This was a simulated sandbox project, not a production deployment.
- Security groups represented access; they were not connected to production applications or data.
- HR triggers and approvals were simulated rather than integrated with an HR system or ticketing platform.
- Access expiration was simulated in compressed time rather than waiting 30 calendar days.
- No privileged Entra roles were assigned to the test identities.

## What I Learned

- How to create and manage internal and external identities in Microsoft Entra ID.
- How to model birthright, departmental, role-specific, and guest access with security groups.
- How to update identity attributes and manager relationships during lifecycle events.
- How to prevent access creep by removing obsolete permissions during a transfer.
- How to disable accounts, revoke sessions, and preserve identity records during offboarding.
- How to invite, redeem, validate, and expire a B2B guest identity.
- How to use Entra audit logs and screenshots as supporting evidence.

## What I Would Do Differently in Production

I would not rely on an IAM administrator manually receiving employment information. I would use an authoritative HR source, ticketed approvals, application-owner validation, automated provisioning where appropriate, expiration enforcement for external access, staged testing, monitoring, and formally retained audit evidence. Privileged changes would require stronger separation of duties and independent review.

## Resume Bullet

- Designed and executed a simulated Joiner–Mover–Leaver lifecycle in a Microsoft Entra ID P2 sandbox, using group-based access to provision a Finance joiner, remove stale permissions during a Sales-to-HR transfer, deprovision a leaver, and manage time-limited B2B guest access; validated changes through identity attributes, membership reviews, session revocation, and audit logs.

## 60-Second Interview Explanation

> I built an independent Joiner–Mover–Leaver lab in Microsoft Entra ID for a fictional organization. I created an access model using security groups for employee, department, job-function, and guest access. I then processed four lifecycle cases: a Finance joiner, a Sales-to-HR mover, an Operations leaver, and a time-limited external auditor. The key decision was to treat a mover as both provisioning and deprovisioning, so I removed Jordan's obsolete Sales access instead of only adding HR access. For the leaver and expired guest, I disabled the accounts, revoked sessions, removed entitlements, and retained the identity records. I validated the outcomes through profile attributes, group memberships, account status, and Entra audit logs. This was sandbox work rather than production experience, but it gave me hands-on practice making and verifying the kinds of lifecycle decisions an IAM analyst performs.

## Skills Demonstrated

Microsoft Entra ID · Joiner–Mover–Leaver · Identity lifecycle management · User provisioning and deprovisioning · B2B guest identities · Security groups · Role-based access control · Least privilege · Session revocation · Access validation · Audit logs · IAM documentation
