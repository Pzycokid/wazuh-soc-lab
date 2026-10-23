# Playbook: Suspicious PowerShell (rule 100120)

**Severity:** High
**ATT&CK:** T1059.001 PowerShell

## 1. Triage
- [ ] Read the full `scriptBlockText`. Does it download something, decode Base64, or run in memory?
- [ ] Record the host, user, parent process (from Sysmon Event 1) and time.

## 2. Investigate
- [ ] Decode any Base64 content (CyberChef, in an isolated environment) and note URLs, IPs and file names.
- [ ] Check every URL / IP / hash against threat intel (or let BlueShield enrich it).
- [ ] Look at Sysmon Event 3 (network connections) and Event 11 (file creation) around the same time.
- [ ] Was this launched by Office, a browser, or a script host? That suggests phishing.

## 3. Contain
- [ ] Isolate the host from the network if a payload was downloaded or executed.
- [ ] Block the identified domains / IPs at the proxy or firewall.

## 4. Recover and improve
- [ ] Collect memory and disk artefacts before reimaging if the incident is confirmed.
- [ ] Consider Constrained Language Mode and AppLocker / WDAC policies for PowerShell.

## False-positive checks
Admin and deployment scripts (SCCM, Intune, Chocolatey) that legitimately download and run content.
Allow-list them by signer or script path once confirmed.
