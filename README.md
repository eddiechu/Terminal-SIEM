# **Terminal SIEM**
> [!NOTE]
> under construction ...\
> Help administrator without SIEM or with SIEM but limited capability.

## **Characteristics**
Attribute | Termina SIEM | Community \/ Brand SIEM
--- | --- | ---
Technology | **Linux terminal, file based** | Web based, index and NoSQL
Production architecture | **ONE host** | Several hosts per role
Sizing | **xlarge** | 4xlarge per host
Log injection | **Hundreds thousand entries / sec (*TCP is recommanded*)** | Thousand, need MQ to support more volume to minimize log loss burst
Multiprocessing | **Yes** | Depend
Correlation | **Yes** | Yes
Log parsing | **by awk, jq or Golang, build from scratch, with the help of Gen AI<br/>(*Can avoid regular experssion for hundreds times performance gain*)** | built-in parser for common log source
Threat detection | **by grep or awk, build from scratch, convert from community rules with the help of Gen AI** | Primitive, need to build most
Flexibility | **Can develop search criteria what ever you think, and with the help of Gen AI, <br/>e.g. <ul><li>any source IP to a destinated IP access more than 20 different ports</li><li>any user login O365 cross more than one countries within an hour</li><li>over 80 unique user login failed over an hour</li><li>what happen on that laptop and firewall in 3 minutes before the incident</li><li>any user to a single web site, accumulated upload size more than 100MB within a day</li><li>accumulate web access time per host per desgination within a day to spot potential C2</li></ul>** | Limit to the product capability
High availability | **Load balancers, nodes and share storage** | Can extend to HA
Scalability | **Vertical or horizontal** | Horizontal
Dashboard and report | **No** | Yes
Access control | **Linux** | Product feature
Patch / security management | **Linux** | Linux and product
Store raw log | **Yes, raw log and parsed log** | No
Retention | **File management** | Index management
Skill set required | **Linux rsyslog, grep, awk, jq, find, sort, uniq, parallel, xargs, file management, etc (*may need Golang and Python for advanced usage*)** | Product knowledge

## **Cheat Sheet**

### <ins>Log Collection</ins>
:bookmark:  **Consolidate all syslog sources to a single file, use timestamp as file name**

in the /etc/rsyslog.conf, configure to use TCP (minimize log loss) and timestamp as file name, e.g. rsyslog-202507020420.log

```
module(load="imtcp")
input(type="imtcp" port="514")

$template CustomTemplate, "/var/rsyslog/rsyslog-%$YEAR%%$MONTH%%$DAY%%$HOUR%%$MINUTE%.log"
*.* ?CustomTemplate;RSYSLOG_TraditionalFileFormat

$ActionFileDefaultTemplate RSYSLOG_TraditionalFileFormat
```
then restart rsyslog service to take effective

### <ins>Log Parsing</ins>
:bookmark:  **Parse syslog to standard schema with delimiator of "|"**

> syslog example
> 
> Jul  2 11:59:57 firewall.office.local logver=904012795 timestamp=1751457595 devname="office-fw01" devid="TGVM8VTM20000284" vd="root" date=2025-07-02 time=11:59:55 eventtime=1751457595817641505 logid="0000000019" type="traffic" subtype="forward" level="notice" srcip=192.168.13.87 srcport=52944 srcintf="port2" srcintfrole="undefined" dstip=34.120.146.18 dstport=443 dstintf="port1" dstintfrole="undefined" srccountry="Reserved" dstcountry="United States" sessionid=2897923 proto=6 action="server-rst" policyid=1 policytype="policy" poluuid="c3754272-8221-51ef-2830-20a9f9ffa552" policyname="Allow to Internet" service="HTTPS" trandisp="snat" transip=192.168.90.5 transport=52944 appid=57465 app="Palo.Alto.Networks.Cortex.XDR" appcat="General.Interest" apprisk="elevated" applist="block-high-risk" duration=5 sentbyte=4100 rcvdbyte=10370 sentpkt=13 rcvdpkt=16 utmaction="allow" countapp=1 tz="+0000"

`parse.sh`
``` 
#!/bin/bash
while IFS= read -r line; do
  event_time=$(echo "$line" | grep -oP 'timestamp=\K[^ ]+')
  source_ip=$(echo "$line" | grep -oP 'srcip=\K[^ ]+')
  target_ip=$(echo "$line" | grep -oP 'dstip=\K[^ ]+')
  target_port=$(echo "$line" | grep -oP 'dstport=\K[^ ]+')
  event_action=$(echo "$line" | grep -oP 'utmaction="\K[^" ]+')
  echo "event_time:$event_time|source_ip:\"$source_ip\"|target_ip:\"$target_ip\"|target_port:$target_port|event_action:\"$event_action\""
done
```
`\K` only output what comes after\
`[` Starts a character set\
`^ ` end delimiter, e.g. space
`^"` end delimiter, e.g. quote\
`]` Ends the charcter set\
`+` match all characters
```
tail rsyslog.log | parse.sh >> parsedlog.db
```
> event_time:`1751457595`|source_ip:`192.168.13.87`|target_ip:`34.120.142.18`|target_port:`443`|event_action:`"allow"`

### <ins>Threat hunting \/ detection</ins>
:bookmark:  **Search "mimikatz" form the log since last check**
``` 
for f in `find \var\log\rsyslog\rsyslog*.log -mmin -3`; do pos=$(cat ${f}.lastpos); lastpos=$(stat -c %s ${f}); echo $lastpos > ${f}.lastpos; tail -c +$pos ${f} | xargs -P 0 -I {} sh -c 'echo "{}" | grep -i "mimikatz"'; done
```
OR
``` 
for f in `find \var\log\rsyslog\rsyslog*.log -mmin -3`;
do pos=$(cat ${f}.lastpos); 
  lastpos=$(stat -c %s ${f}); 
  echo $lastpos > ${f}.lastpos;

  tail -c +$pos ${f} | xargs -P 0 -I {} sh -c 'echo "{}" | grep -i "mimikatz"'; 
done
```
`find ... -mmin -3` find file last modified within 3 minutes\
`stat -c %s ...` total characters in the file\
`echo $lastpost ...` store the total characters as last position in the .pos file\
`tail -c +$pos ...` get the content after number of characters \/ since last check\
`xargs -P 0 ...` run in multiple processes\
`grep -i ...` search text, case in-sensitive

> This way, you can run it by schedule, keep tracking last check position, no overlap happen

To archive `tail` .. `grep` in multiple processes, either
```
tail rsyslog.log | xargs -P 0 -I {} sh -c 'echo "{}" | grep -i "mimikatz"'
```
OR
```
tail rsyslog.log | parallel -j 0 --pipe grep -i "mimikatz"'
``` 
`parallel -j 0 ...` run in multiple processes
OR
> Multiple searches in one batch

`detection.sh`
```
#!/bin/bash
while IFS= read -r line; do
  echo "$line" | grep -i "mimikatz"
  echo "$line" | grep -i "rm" | grep -i ".bash_history"
done
```
```
tail rsyslog.log | xargs -P 0 -I {} sh -c 'echo "{}" | detection.sh'
```
OR
```
tail rsyslog.log | parallel -j 0 --pipe detection.sh'
``` 


#
:bookmark:  **example 2**

> sample log content

``` 
command line
```
`-r` parameter 1\
`-t` parameter 2
> result 1\
> result 2

#
<!-- siem
opensearch
elasticsearch
search
index
security
-->
