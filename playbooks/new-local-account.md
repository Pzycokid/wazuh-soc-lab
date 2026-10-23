# Playbook: New Local Account / Admin Group Change (rules 100110 / 100111)

**Severity:** 100110 = Medium · 100111 = **High**
**ATT&CK:** T1136.001 Create Local Account, T1098 Account Manipulation

## 1. Triage
- [ ] Record the new account name, the account that created it (`subjectUserName`), the host and the time.
- [ ] Is there an approved change ticket or a known admin action at that time?

## 2. Investigate
- [ ] Was the creator account itself recently created or used from an unusual host?
- [ ] Look for events just before: suspicious PowerShell (100120), RDP logons (4624 LogonType 10), new services.
- [ ] Has the new account logged in anywhere yet (4624 for that username)?

## 3. Contain
- [ ] If unapproved: disable the account (`net user <name> /active:no`) and remove it from Administrators.
- [ ] Reset the password of the creator account if it looks compromised.

## 4. Recover and improve
- [ ] Remove the account after evidence is collected.
- [ ] Review who has rights to create local accounts; prefer LAPS / central identity management.

## False-positive checks
IT onboarding, software installers that create service accounts, approved admin work.
