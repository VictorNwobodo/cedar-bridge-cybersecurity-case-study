# Week 1 Analyst Workbook
**Case:** Cedar Bridge CB-001  
**Prepared by:** Nwobodo Chukwuemeka Victor (SOC L1 Analyst)

**Route used for Lab 3:** Supplied reference outputs (no-install route)  
**Evidence status:** Fictional teaching evidence

Keep this workbook private with the learning evidence. Do not treat the example addresses as real targets or scan them.

---

# Lab 1 | Asset and risk notes

| Asset ID | Asset | Owner | Primary security need | Plausible threat | Weakness / vulnerability | Possible impact | Proposed control |
|---|---|---|---|---|---|---|---|
| CUSTOMER01 | Customer contact list | Customer service | Confidentiality | An unauthorised person reads customer details | Access permissions may be broader than necessary | Exposure of customer information, privacy concerns and loss of customer trust | Apply role-based access and review access logs |
| BILL01 | Invoice register | Finance | Integrity | An unauthorised user changes invoice amounts | Editing permissions or change monitoring may be insufficient | Incorrect bills, financial loss, customer disputes and unreliable records | Restrict editing to authorised finance staff and keep an audit trail of changes |
| PAY01 | Payroll export | Finance manager | Confidentiality | An unauthorised user attempts to access or download payroll information | MFA is optional for Amara's account; this is a weakness, not proof of exploitation | Disclosure of employee salary information and reputational or privacy harm | Enforce MFA and least-privilege access; log and review payroll downloads |

**Security objective notes**
- **Confidentiality:** Information is available only to authorised people.
- **Integrity:** Information remains accurate and protected from improper change.
- **Availability:** Systems and information are usable when needed.

---

# Ten-term glossary

| Term | Meaning | Cedar Bridge example |
|---|---|---|
| **Asset** | Something valuable that an organisation needs to protect. | `PAY01` is an asset because payroll information must be protected. |
| **Threat** | A potential cause of harm to an asset. | An unauthorised person attempting to access Amara's account is a possible threat. |
| **Vulnerability** | A weakness that could be exploited by a threat. | MFA being optional for Amara's account is a vulnerability. |
| **Risk** | The possibility that a threat will exploit a weakness and cause harm; it considers likelihood and impact. | Optional MFA increases the risk of unauthorised access to Amara's account. |
| **Confidentiality** | Protecting information from unauthorised access or disclosure. | Only the `finance_manager` role may download `PAY01`. |
| **Integrity** | Protecting information from unauthorised or improper changes. | Invoice amounts must remain accurate and changes traceable. |
| **Availability** | Keeping systems or information usable when required. | Customers need `PORTAL01` online to place orders during business hours. |
| **Authentication** | Checking that a user or account has successfully established an identity. | A wrong password causes a failed login; a valid login creates a session. |
| **Authorization** | Deciding what an authenticated account is allowed to access or do. | Amara's `billing_clerk` role is denied permission to download `PAY01`. |
| **Evidence** | Information or records that support or challenge an observation or conclusion. | The `09:14:00` log line records a denied `PAY01` download; it does not prove that data was stolen. |

---

# Lab 2 | Connection diagram

## Supplied connection details

- Laptop: `CB-LT-07`
- Laptop private IP: `10.20.0.24`
- Default gateway/router: `10.20.0.1`
- Configured DNS resolver: `10.20.0.53`
- Requested name: `portal.cedarbridge.example`
- DNS answer: `portal.cedarbridge.example A 192.0.2.80`
- HTTPS destination: `192.0.2.80:443/TCP`
- Office public egress/NAT address: `203.0.113.10`
- Firewall decision: `action=allow`
- HTTP response inside the encrypted session: `200 OK`

## Cedar-Bridge-Network Diagram
<img width="1326" height="651" alt="Cedar-Bridge-Network-Diagram drawio" src="https://github.com/user-attachments/assets/e3eac3bf-cadd-4b63-97a2-265f2abfbf76" />



**DNS explanation:** The laptop asks the configured resolver `10.20.0.53` for the address associated with `portal.cedarbridge.example`. The supplied response returns `192.0.2.80`. DNS resolves a name to a record; it is a separate exchange from the later website connection.

**Website connection explanation:** The browser then connects from `10.20.0.24:51514` to `192.0.2.80:443` using TCP and HTTPS. The default gateway routes traffic beyond the local network. The office router/NAT uses the documented public egress address `203.0.113.10`. The firewall record says the connection was allowed. Because TLS decryption is not configured, the supplied firewall excerpt shows connection metadata, not the plaintext webpage content. An allowed connection or an HTTPS connection does not prove that the application or its users are harmless.

## Authentication versus authorization

- **Failed authentication:** A wrong password results in `result=failed`; the identity check does not succeed, so an authenticated session is not established by that attempt.
- **Denied authorization:** Amara has an authenticated session with the `billing_clerk` role, but a request to download `PAY01` is denied because only `finance_manager` is allowed to download payroll.

MFA strengthens authentication by adding an independent factor. It does not grant additional roles or permissions.

---

# Lab 3 | Linux evidence

**Route used: supplied reference outputs (reference-output route).**  
I used the supplied simulated terminal output and full log. I did not run these commands on my own Ubuntu machine and do not present the supplied process snapshot as my own machine's current process list.

| Command | Observed result from supplied reference | What it tells me |
|---|---|---|
| `pwd` | `/home/analyst/week1` | Shows the current working directory in the simulated reference output. |
| `ls` | `README.txt case.txt login.log processes.txt reference-terminal.txt workbook.md` | Lists the files in the simulated working directory. |
| `cat login.log` | The supplied full log contains 7 event lines. | Displays the complete supplied account log. |
| `grep 'result=failed' login.log` | Returns the three Amara failed-login lines at 09:12:00, 09:12:20 and 09:12:40 WAT. | Filters the log to lines containing `result=failed`. |
| `wc -l login.log` | `7 login.log` | Counts seven lines in the supplied log. |
| `ls -l login.log` | `-rw-r----- 1 analyst analysts 744 Oct 6 09:30 login.log` | Shows file mode, owner, group, size and modification time in the reference output. |
| `cat processes.txt` | `410 root sshd`; `811 analyst bash`; `912 analyst python3` (plus the header). | Displays a saved process snapshot. PID `912` is assigned to `python3` in that snapshot. |
| `ps -eo pid,comm \| head` | No personal-machine output supplied. | Would show process IDs and command names on the machine where it is run, with output limited by `head`. The real process list can differ from the saved snapshot. |

## File mode interpretation

Supplied mode: `-rw-r-----`

- First character `-`: regular file.
- Owner section `rw-`: owner can read and write.
- Group section `r--`: group can read, but cannot write or execute.
- Others section `---`: everyone else has no read, write or execute permission.
- No execute permission is granted by this mode.

These permissions restrict access, but they do not by themselves guarantee log integrity because the owner can write to the file. The supplied owner/group names and mode are examples and may differ after real extraction.

## Process snapshot interpretation

The supplied snapshot lists `sshd` (PID 410, user `root`), `bash` (PID 811, user `analyst`) and `python3` (PID 912, user `analyst`). A process name alone is not enough to classify a process as malicious. Useful context would include its executable path, parent process, command line, owner and expected business purpose. No process should be stopped based only on this snapshot.

## Failed-login excerpt

```text
2026-10-06T09:12:00+01:00 user=amara device=unknown src=198.51.100.77 result=failed reason=wrong_password
2026-10-06T09:12:20+01:00 user=amara device=unknown src=198.51.100.77 result=failed reason=wrong_password
2026-10-06T09:12:40+01:00 user=amara device=unknown src=198.51.100.77 result=failed reason=wrong_password
```

The excerpt shows three failed password attempts from the same unfamiliar source and unknown device within 40 seconds.

---

# Case CB-001 | Initial incident handover

## 1. Summary

On 6 October 2026, Amara's account recorded three failed password attempts from `198.51.100.77`, followed by a successful login from the same unfamiliar source and an unknown device. The successful event recorded `mfa=not_required`. One minute later, a request to download `PAY01` was denied because the account had the `billing_clerk` role. The sequence is anomalous and requires further investigation, but the supplied records do not establish who operated the session or prove that payroll data was accessed or stolen.

## 2. Timeline and supporting evidence

All timestamps below retain the supplied `+01:00` offset, which represents WAT.

| Time (WAT) | Observation | Supplied source line / excerpt |
|---|---|---|
| 09:05:00 | Amara successfully logged in from assigned laptop `CB-LT-07` and office egress address; MFA passed. | `2026-10-06T09:05:00+01:00 user=amara device=CB-LT-07 src=203.0.113.10 result=success mfa=passed` |
| 09:12:00 | First failed login for Amara from unknown device/source due to wrong password. | `2026-10-06T09:12:00+01:00 user=amara device=unknown src=198.51.100.77 result=failed reason=wrong_password` |
| 09:12:20 | Second failed login from the same unknown device/source. | `2026-10-06T09:12:20+01:00 user=amara device=unknown src=198.51.100.77 result=failed reason=wrong_password` |
| 09:12:40 | Third failed login from the same unknown device/source. | `2026-10-06T09:12:40+01:00 user=amara device=unknown src=198.51.100.77 result=failed reason=wrong_password` |
| 09:13:10 | Successful login from the same unfamiliar source/device; MFA was not required. | `2026-10-06T09:13:10+01:00 user=amara device=unknown src=198.51.100.77 result=success mfa=not_required` |
| 09:14:00 | A download request for `PAY01` was denied because the account role was `billing_clerk`. | `2026-10-06T09:14:00+01:00 user=amara device=unknown src=198.51.100.77 action=download resource=PAY01 result=denied role=billing_clerk` |
| 09:20:00 | Ben successfully logged in from `CB-LT-08` and the documented office egress address; MFA passed. | `2026-10-06T09:20:00+01:00 user=ben device=CB-LT-08 src=203.0.113.10 result=success mfa=passed` |

## 3. Findings

1. **Repeated failed authentication:** Three wrong-password attempts against Amara's account occurred between 09:12:00 and 09:12:40 WAT.
2. **Unfamiliar successful session:** At 09:13:10, the same source and unknown device successfully authenticated as Amara. The supplied office baseline shows an earlier successful session from `203.0.113.10` on `CB-LT-07`.
3. **MFA not required:** The successful unfamiliar session records `mfa=not_required`. The inventory context says MFA is optional for Amara's account. This is a weakness, not proof that it was exploited.
4. **Payroll download denied:** At 09:14:00, the account attempted to download `PAY01`; the request was denied because `billing_clerk` is not the permitted `finance_manager` role.
5. **No confirmed data theft:** The supplied log shows a denied download. It does not show a successful payroll download, data exfiltration or who was operating the session.

## 4. Risk and impact

The primary concern is potential unauthorised use of Amara's account. If the session was unauthorised, it could expose information available to her account. The unfamiliar source, unknown device, repeated failed attempts followed by success, and MFA not being required justify further investigation.

The denied payroll request indicates that role-based authorization blocked that specific download attempt. It does not prove that every other resource was protected or that no other activity occurred.

## 5. Hypotheses and unknowns

**Hypothesis A — Unauthorised access:** Someone may have obtained or guessed Amara's password and authenticated without an MFA challenge.

**Hypothesis B — Legitimate activity from an unusual device or network:** Amara may have used an unrecorded device, remote connection or network.

The supplied evidence does not distinguish these explanations. An unfamiliar IP does not identify a person, prove a physical location, or by itself prove compromise.

## 6. Additional evidence requested

- **Account/session records:** Detailed authentication events, session identifiers and duration, MFA decision/reason, and activity linked to the successful session.
- **Endpoint/device records:** Device identity, hostname, endpoint telemetry and relevant login/activity records for the unknown device/session.
- **Network/VPN records:** Relevant VPN/remote-access records and firewall/network connection records around 09:12–09:14 WAT.
- **User confirmation:** Confirm Amara's activity through an approved organisational channel.
- **Access records:** Relevant application/audit records to establish whether any payroll access or transfer succeeded beyond the supplied denied request.

Preserve the original log lines and timestamps during review.

## 7. Recommendation to supervisor

I recommend keeping CB-001 open and escalating for authorised evidence collection and review. Request the account/MFA/session, endpoint/device and network/VPN records listed above, then compare them with the supplied office baseline and confirm the context with Amara through an approved channel. Do not reset the account, block the source, contact third parties or scan systems without authorisation. A supervisor or authorised incident responder should decide whether containment or other remediation is justified after reviewing the evidence.

## 8. Initial assessment

**Status:** Open — further evidence required  
**Assessment:** Suspicious authentication sequence; possible unauthorised account use, not confirmed  
**Confidence:** High that the supplied log contains the stated sequence; insufficient evidence to attribute the activity to a person or confirm account compromise.

### Confirmed from supplied evidence
- Three failed logins for Amara.
- A subsequent successful login from the same unfamiliar source and unknown device.
- MFA was not required for that successful login.
- A `PAY01` download request was denied for the `billing_clerk` role.

### Not confirmed
- Who operated the session.
- Whether Amara authorised the login.
- Whether credentials were compromised.
- Whether payroll data was successfully accessed or stolen.

---
