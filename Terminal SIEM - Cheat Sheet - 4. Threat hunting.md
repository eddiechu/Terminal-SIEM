# **Terminal SIEM - Cheat Sheet - Threat hunting**

## :bookmark:  **Search threat patterns form the syslog or parsed log**

```bash
tail rsyslog.log | xargs -P 0 -I {} sh -c 'echo "{}" | grep -i "mimikatz"'
tail parsedlog.dat | xargs -P 0 -I {} sh -c 'echo "{}" | grep -i "log_type=firewall" | grep -i "source_ip=192.168.21.37"'
```
`xargs -P 0 ...` run in multiple processes utilize all processors

OR
```bash
tail rsyslog.log | parallel -j 0 --pipe grep -i "mimikatz"
tail parsedlog.dat | parallel -j 0 --pipe 'grep -i "log_type=firewall" | grep -i "source_ip=192.168.21.37"'
```
`parallel -j 0 ...` run in multiple processes utilize all processors

---
<br />
<br />
<br />

## :bookmark:  **Search threat patterns within date range**

```bash
find rsyslog*.log -type f -newermt "2045-05-01 00:00:00" \! -newermt "2045-05-02 00:00:00" | xargs -P 0 -n 1 grep -i "mimikatz"
find parsedlog*.dat -type f -newermt "2045-05-01 00:00:00" \! -newermt "2045-05-02 00:00:00" | xargs -P 0 -n 1 grep -i "log_type=firewall" | grep -i "source_ip=192.168.21.37"
```
`xargs -P 0 ...` run in multiple processes utilize all processors

OR
```bash
find rsyslog*.log -type f -newermt "2045-05-01 00:00:00" \! -newermt "2045-05-02 00:00:00" | parallel -j 0 grep -i "mimikatz"
find parsedlog*.dat -type f -newermt "2045-05-01 00:00:00" \! -newermt "2045-05-02 00:00:00" | parallel -j 0 'grep -i "log_type=firewall" | grep -i "source_ip=192.168.21.37"'
```
`parallel -j 0 ...` run in multiple processes utilize all processors

---
<br />
<br />
<br />

## :bookmark:  **Search threat patterns in last 30 minutes**

```bash
find rsyslog*.log -type f -maxdepth 1 -mmin -30 | xargs -P 0 -n 1 grep -i "mimikatz"
find parsedlog*.dat -type f -maxdepth 1 -mmin -30 | xargs -P 0 -n 1 grep -i "log_type=firewall" | grep -i "source_ip=192.168.21.37"
```
`xargs -P 0 ...` run in multiple processes utilize all processors

OR
```bash
find rsyslog*.log -type f -maxdepth 1 -mmin -30 | parallel -j 0 grep -i "mimikatz"
find parsedlog*.dat -type f -maxdepth 1 -mmin -30 | parallel -j 0 'grep -i "log_type=firewall" | grep -i "source_ip=192.168.21.37"'
```
`parallel -j 0 ...` run in multiple processes utilize all processors

---
<br />
<br />
<br />

## :bookmark:  **Aggragate login failed per user**

```bash
find rsyslog*.log -type f -maxdepth 1 -mmin -30 | xargs -P 0 -n 1 grep -i "mimikatz"
find parsedlog*.dat -type f -maxdepth 1 -mmin -30 | xargs -P 0 -n 1 grep -i "log_type=firewall" | grep -i "source_ip=192.168.21.37"

grep -i "bad password" filteradp-*.txt | grep -i "username" | grep -v "CLIENT_IP_ADDRESS = 172.30.40.18\|CLIENT_IP_ADDRESS = 172.30.40.77\|CLIENT_IP_ADDRESS = 172.30.40.78\|CLIENT_IP_ADDRESS = 172.18.30.11" | 
printf "%s %s\n" $(echo "$line" | awk -F'source_ip=' '{print $2}' | awk -F'|' '{print $1}') $(echo "$line" | awk -F'target_ip=' '{print $2}' | awk -F'|' '{print $1}') | sort | uniq -c

```
`xargs -P 0 ...` run in multiple processes utilize all processors

OR
```bash
find rsyslog*.log -type f -maxdepth 1 -mmin -30 | parallel -j 0 grep -i "mimikatz"
find parsedlog*.dat -type f -maxdepth 1 -mmin -30 | parallel -j 0 'grep -i "log_type=firewall" | grep -i "source_ip=192.168.21.37"'
```
`parallel -j 0 ...` run in multiple processes utilize all processors


<!-- 
event id 4771
Account Name:	
IpAddress
Keywords: Audit Failure
Client Address
Kerberos pre-authentication failed.



CommandLine "C:\Users\user1\Desktop\SysinternalsSuite\ADExplorer64.exe"  
 User LAB\user1 
 Process Create:
Process Create (rule: ProcessCreate)


 
Process Create:
QueryName: elasticpoint.net
task category Dns query (rule: DnsQuery)



Network connection detected:
DestinationIp: 192.168.157.131
DestinationHostname: -
DestinationPort: 3389
Image: C:\Windows\System32\mstsc.exe
User: LAB\user1
SourceIp: 192.168.157.130

Source: Sysmon

https://www.gnu.org/software/parallel/parallel_examples.html#example-parallel-grep
-->
