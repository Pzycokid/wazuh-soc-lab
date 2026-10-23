# Playbook: SSH Brute Force (rules 100100 / 100101)

**Severity:** 100100 = Medium · 100101 = **High** (possible compromise)
**ATT&CK:** T1110.001 Password Guessing, T1078 Valid Accounts

## 1. Triage (first 5 minutes)
- [ ] Note the source IP (`srcip`), target host, target username(s) and time window.
- [ ] Is the source IP internal or external? Internal sources are more urgent: it may be a compromised host.
- [ ] Did rule **100101** fire for the same IP? If yes, treat as a **likely compromise** and jump to Containment.

## 2. Investigate
- [ ] Count failed attempts and distinct usernames tried. Many usernames = spraying; one username = targeted.
- [ ] Check the IP's reputation (AbuseIPDB, VirusTotal) or let BlueShield enrich it automatically.
- [ ] Search for any other activity from the same IP across all agents in the last 24 h.
- [ ] If a login succeeded: what did the session do? Check `auth.log`, shell history, new processes, new cron jobs.

## 3. Contain
- [ ] Block the source IP at the host firewall (`ufw deny from <ip>`) or perimeter.
- [ ] If a login succeeded: disable or reset the account, kill active sessions (`pkill -u <user>`).

## 4. Recover and improve
- [ ] Enforce key-based SSH auth and set `PasswordAuthentication no`.
- [ ] Consider `fail2ban` or Wazuh active response for automatic blocking.
- [ ] Document the timeline and close the ticket.

## False-positive checks
A user who forgot their password, or an automation job with stale credentials. Confirm with the account owner.
