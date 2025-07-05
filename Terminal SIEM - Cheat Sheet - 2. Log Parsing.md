# **Terminal SIEM - Cheat Sheet - Log Parsing**

## :bookmark:  **Parse syslog to standard schema with delimiator of "|"**

> syslog example
> 
> `Jul  2 11:59:57` firewall.office.local logver=904012795 timestamp=`1751457595` devname="office-fw01" devid="TGVM8VTM20000284" vd="root" date=2045-07-02 time=11:59:55 eventtime=1751457595817641505 logid="0000000019" type="traffic" subtype="forward" level="notice" srcip=`192.168.13.87` srcport=52944 srcintf="port2" srcintfrole="undefined" dstip=`34.120.146.18` dstport=`443` dstintf="port1" dstintfrole="undefined" srccountry="Reserved" dstcountry="United States" sessionid=2897923 proto=6 action="server-rst" policyid=1 policytype="policy" poluuid="c3754272-8221-51ef-2830-20a9f9ffa552" policyname="Allow to Internet" service="HTTPS" trandisp="snat" transip=192.168.90.5 transport=52944 appid=57465 app="Palo.Alto.Networks.Cortex.XDR" appcat="General.Interest" apprisk="elevated" applist="block-high-risk" duration=5 sentbyte=4100 rcvdbyte=10370 sentpkt=13 rcvdpkt=16 utmaction="`allow`" countapp=1 tz="+0000"

e.g. extract source IP from the syslog\
between `srcip=` and ` ` (space) :arrow_right: `awk -F'`srcip=`' '{print $2}' | awk -F'` `' '{print $1}')`\
or\
between `srcip=` and ` srcport=` :arrow_right: `awk -F'`srcip=`' '{print $2}' | awk -F'` srcport=`' '{print $1}')`

:page_facing_up: `parse.sh` 
```bash
#!/bin/bash
while IFS= read -r line; do
  if [ "$line" =~ "logver=" ] && [ "$line" =~ "timestamp=" ] && [ "$line" =~ "dstintfrole=" ]; then
    log_type="firewall"
    log_time=$(echo "$line" | awk -F' ' '{print $1 " " $2i " " $3}')
    event_time=$(echo "$line" | awk -F'timestamp=' '{print $2}' | awk -F' ' '{print $1}')
    source_ip=$(echo "$line" | awk -F'srcip=' '{print $2}' | awk -F' ' '{print $1}')
    target_ip=$(echo "$line" | awk -F'dstip=' '{print $2}' | awk -F' ' '{print $1}')
    target_port=$(echo "$line" | awk -F'dstport=' '{print $2}' | awk -F' ' '{print $1}')
    event_action=$(echo "$line" | awk -F'utmaction="' '{print $2}' | awk -F'" ' '{print $1}')
    echo "log_time:$log_time|log_type:$log_type|event_time:$event_time|source_ip:\"$source_ip\"|target_ip:\"$target_ip\"|target_port:$target_port|event_action:\"$event_action\""
  fi
done
```
> [!TIP]
>Regular expression like `grep -oP 'utmaction="\K[^" ]+'` is not recommanded, because of less efficient

```bash
tail rsyslog.log | parse.sh >> parsedlog-$(date +%Y%m%d%H%M).dat
```
:page_facing_up: `parsedlog-204507021159.dat`
> log_time:`Jul 2 11:59:57`|log_type:`firewall`|event_time:`1751457595`|source_ip:`192.168.13.87`|target_ip:`34.120.142.18`|target_port:`443`|event_action:`"allow"`

---
## :bookmark:  **Parse syslog since last check**
```bash
for f in `find \var\log\rsyslog\rsyslog*.log -mmin -3`; do pos=$(cat ${f}.lastpos); lastpos=$(stat -c %s ${f}); echo $lastpos > ${f}.lastpos; tail -c +$pos ${f} | xargs -P 0 -I {} sh -c 'echo "{}" | parse.sh >> parsedlog-$(date +%Y%m%d%H%M).dat'; done
```
OR
```bash
for f in `find \var\log\rsyslog\rsyslog*.log -mmin -3`;
do pos=$(cat ${f}.lastpos); 
  lastpos=$(stat -c %s ${f}); 
  echo $lastpos > ${f}.lastpos;

  tail -c +$pos ${f} | xargs -P 0 -I {} sh -c 'echo "{}" | parse.sh >> parsedlog-$(date +%Y%m%d%H%M).dat'; 
done
```
`find ... -mmin -3` find file last modified within 3 minutes\
`stat -c %s ...` total characters in the file\
`echo $lastpost ...` store the total characters as last position in the .pos file\
`tail -c +$pos ...` get the content after number of characters \/ since last check\
`xargs -P 0 ...` run in multiple processes

This way, you can run it by schedule, keep tracking last check position, avoid duplication

> [!TIP]
> another way to have multiple processing, Linux GNU parallel

```bash
for f in `find \var\log\rsyslog\rsyslog*.log -mmin -3`; do pos=$(cat ${f}.lastpos); lastpos=$(stat -c %s ${f}); echo $lastpos > ${f}.lastpos; tail -c +$pos ${f} | parallel -j 0 --pipe parse.sh >> parsedlog-$(date +%Y%m%d%H%M).dat'; done
```
`parallel -j 0 ...` run in multiple processes utilize all processors

--
