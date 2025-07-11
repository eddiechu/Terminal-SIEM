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

## :bookmark:  **Aggragate unique user login failed in last 30 minutes**

filter log with log_type=windows, log_eventid=4771 and consists "authentication failed"
exclude certain source IP, e.g. =192.168.100.2, =192.168.100.3

```bash
find parsedlog*.dat -maxdepth 1 -mmin -30 | grep -i "log_type=windows" | grep -i "log_eventid=4771" | grep -i "authentication failed" | grep -i -v "source_ip=192.168.100.2\|source_ip=192.168.100.3" | while read -r line; do printf "%s %s\n" $(echo "$line" | awk -F'user=' '{print $2}' | awk -F'|' '{print $1}') $(echo "$line" | awk -F'source_ip=' '{print $2}' | awk -F'|' '{print $1}'); done | sort | uniq -c
```
OR
```bash
find parsedlog*.dat -maxdepth 1 -mmin -30 |
  grep -i "log_type=windows" |
  grep -i "log_eventid=4771" | grep -i "authentication failed" | grep -i -v "source_ip=192.168.100.2\|source_ip=192.168.100.3" |
  while read -r line;
    do printf "%s %s\n" $(echo "$line" | awk -F'user=' '{print $2}' | awk -F'|' '{print $1}') $(echo "$line" | awk -F'source_ip=' '{print $2}' | awk -F'|' '{print $1}');
  done |
  sort | uniq -c
```
>      1 eddie.chu 192.168.100.172
>      1 john.stankey 192.168.100.42
>     27 sundar.pichai 192.168.100.221
>       1 tim.cook 192.168.100.23
>      3 tim.cook 192.168.100.25

If total unique user login failed great than 50
```bash
find parsedlog*.dat -maxdepth 1 -mmin -30 | grep -i "log_type=windows" | grep -i "log_eventid=4771" | grep -i "authentication failed" | grep -i -v "source_ip=192.168.100.2\|source_ip=192.168.100.3" | while read -r line; do printf "%s %s\n" $(echo "$line" | awk -F'user=' '{print $2}' | awk -F'|' '{print $1}') $(echo "$line" | awk -F'source_ip=' '{print $2}' | awk -F'|' '{print $1}'); done | sort | uniq | wc -l
```


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

grep -i "log_type=windows" | grep -i "sysmon" | grep -i "process create" 
grep -i "log_type=windows" | grep -i "sysmon" | grep -i "network connection" 

=10.|=172.16.|=172.17.|=172.18.|=172.19.|=172.20.|=172.21.|=172.22.|=172.23.|=172.24.|=172.25.|=172.26.|=172.27.|=172.28.|=172.29.|=172.30.|=172.31.|=192.168.|=127.|=169.254.

-->
