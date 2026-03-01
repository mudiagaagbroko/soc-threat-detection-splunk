# SPL Queries - Splunk Analysis

### **Detection Query 1: SSH Brute Force Detection**

```
source="auth.log" host="MA-PC" sourcetype="linux_secure"
"Failed password"
| rex "from (?<src_ip>\d+\.\d+\.\d+\.\d+)"
| stats count by src_ip
|where count > 20
| sort -count
| rename count as "Failed Attempts", src_ip as "Source IP", user as "Target User"
```

### **Detection Query 2: Successful Login After Failures**

```
source="auth.log" host="MA-PC" sourcetype="linux_secure"
("Accepted password")
| rex "Accepted password for (?<user>\w+)"
| rex "from (?<src_ip>\d+\.\d+\.\d+\.\d+)"
| search src_ip="172.26.96.1"
| table _time, src_ip, user, _raw
| sort _time
| rename src_ip as "Attacker's IP", user as "Comprised Users", _time as Timestamps, _raw as Events
```


### **Detection Query 3: Escalated Privilege**

```
source="auth.log" host="MA-PC" sourcetype="linux_secure"
sudo
(jdoe OR admin)
| table _time, _raw
| sort _time
| rename _time as "Timestamps", _raw as "Sudo Events"
```


### **Detection Query 4: Persistence Established**
```
source="auth.log" host="MA-PC" sourcetype="linux_secure"
earliest="02/26/2026:21:37:00"
("useradd" OR "usermod")
| table _time, _raw
| sort _time
|rename _time as "Timestamps", _raw as "User Events"
```
