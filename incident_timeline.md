# Incident Timeline – Linux Compromise

This document reconstructs the attacker’s activity chronologically based on log evidence analysed in Splunk.

Log Source: `/var/log/auth.log`  
Attacker IP: 172.26.96.1  

---

## 🔴 Phase 1 – Initial Access

### 🕒 2026-02-26 21:14:01  
Successful SSH login for user `jdoe` from 172.26.96.1  

### 🕒 2026-02-26 21:14:05  
Successful SSH login for user `admin` from 172.26.96.1  

**Observation:**  
Before these logins, 3600 failed SSH attempts were recorded from the same source IP, indicating brute force activity.

**Impact:**  
The attacker obtained valid credentials and established remote access.

---

## 🔴 Phase 2 – Privilege Escalation Attempts

### 🕒 2026-02-26 21:28:29 – 21:29:19  
Multiple failed attempts to switch to root:

```
FAILED SU (to root) jdoe
```

**Observation:**  
The attacker attempted direct root access using the `su` command but failed.

**Impact:**  
Indicates active privilege escalation attempts.

---

## 🔴 Phase 3 – Lateral Movement

### 🕒 2026-02-26 21:36:26  
Successful user switch:

```
su → svc_backup
session opened for user svc_backup by jdoe
```

**Observation:**  
Attacker pivoted from compromised account `jdoe` to `svc_backup` using valid credentials.

**Impact:**  
Demonstrates lateral movement within the host.

---

## 🔴 Phase 4 – Privilege Escalation

### 🕒 2026-02-26 21:37:07  
Root session opened:

```
session opened for user root(uid=0)
```

**Observation:**  
Attacker achieved root-level privileges.

**Impact:**  
Full system compromise confirmed.

---

## 🔴 Phase 5 – Persistence Established

### 🕒 2026-02-26 21:38:41  
New user created:
```
useradd backdoor-user
```

### 🕒 2026-02-26 21:39:20  
New user created:
```
useradd backdoor_user
```

### 🕒 2026-02-26 21:39:59  
Backdoor account granted administrative privileges:
```
usermod add 'backdoor_user' to group 'sudo'
```

**Observation:**  
The attacker created a new privileged account to maintain persistent access.

**Impact:**  
System remains compromised even if original credentials are changed.

---

# ✅ Summary of Attack Progression

| Time | Event | Outcome |
|------|--------|----------|
| 21:14 | SSH brute force success | Initial Access |
| 21:28 | Failed root attempts | Privilege escalation attempt |
| 21:36 | su to svc_backup | Lateral Movement |
| 21:37 | Root access obtained | Full Compromise |
| 21:39 | Backdoor user created | Persistence Established |

---

# 🚨 Overall Impact Assessment

The attacker achieved:

- Remote authenticated access
- Root-level system control
- Persistent privileged account creation

The compromise represents a **critical security incident** requiring immediate remediation.

---
