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
find rsyslog*.log -type f -newermt "2025-05-01 00:00:00" \! -newermt "2025-05-02 00:00:00" | xargs -P 0 -n 1 grep -i "mimikatz"
find parsedlog*.dat -type f -newermt "2025-05-01 00:00:00" \! -newermt "2025-05-02 00:00:00" | xargs -P 0 -n 1 grep -i "log_type=firewall" | grep -i "source_ip=192.168.21.37"
```
`xargs -P 0 ...` run in multiple processes utilize all processors \
`find ... -newermt` from date time `\! -newermt` to date time

OR
```bash
find rsyslog*.log -type f -newermt "2025-05-01 00:00:00" \! -newermt "2025-05-02 00:00:00" | parallel -j 0 grep -i "mimikatz"
find parsedlog*.dat -type f -newermt "2025-05-01 00:00:00" \! -newermt "2025-05-02 00:00:00" | parallel -j 0 'grep -i "log_type=firewall" | grep -i "source_ip=192.168.21.37"'
```
`parallel -j 0 ...` run in multiple processes utilize all processors

---
<br />
<br />
<br />

## :bookmark:  **Search threat patterns in last 30 minutes**

```bash
find rsyslog*.log -maxdepth 1 -mmin -30 | xargs -P 0 -n 1 grep -i "mimikatz"
find parsedlog*.dat -maxdepth 1 -mmin -30 | xargs -P 0 -n 1 grep -i "log_type=firewall" | grep -i "source_ip=192.168.21.37"
```
`xargs -P 0 ...` run in multiple processes utilize all processors \
`find ... -mmin` last modified in minutes

OR
```bash
find rsyslog*.log -maxdepth 1 -mmin -30 | parallel -j 0 grep -i "mimikatz"
find parsedlog*.dat -maxdepth 1 -mmin -30 | parallel -j 0 'grep -i "log_type=firewall" | grep -i "source_ip=192.168.21.37"'
```
`parallel -j 0 ...` run in multiple processes utilize all processors

---
<br />
<br />
<br />

## :bookmark:  **Aggragate unique user login failed in last 30 minutes, alert if over 50**

Filter log with "log_type=windows", "log_eventid=4771" and consists "authentication failed"
Exclude certain source IP, e.g. "=192.168.100.2", "=192.168.100.3"

```bash
find parsedlog*.dat -maxdepth 1 -mmin -30 | \
  grep -i "log_type=windows" | \
  grep -i "log_eventid=4771" | grep -i "authentication failed" | grep -i -v "source_ip=192.168.100.2\|source_ip=192.168.100.3" | \
  while read -r line; \
    do printf "%s %s\n" \
      $(echo "$line" | awk -F'user=' '{print $2}' | awk -F'|' '{print $1}') \
      $(echo "$line" | awk -F'source_ip=' '{print $2}' | awk -F'|' '{print $1}'); \
  done | \
  sort | uniq -c
```
`find ...` filter files wihtin specified time frame :arrow_right: `grep ...` filter specified content :arrow_right: `grep -v ...` exclude not related IP addresses :arrow_right: `printf ...` show the matched "user" and "source_ip" :arrow_right: `sort | uniq -c` group all matched "user" "source_ip" pairs
>      1 eddie.chu 192.168.100.172
>      1 john.stankey 192.168.100.42
>     27 sundar.pichai 192.168.100.221
>       1 tim.cook 192.168.100.23
>      3 tim.cook 192.168.100.25

If total unique user login failed great than 50
```bash
#!/bin/bash

if [[ $(find parsedlog*.dat -maxdepth 1 -mmin -30 | \
  grep -i "log_type=windows" | grep -i "log_eventid=4771" | grep -i "authentication failed" | grep -i -v "source_ip=192.168.100.2\|source_ip=192.168.100.3" | \
  while read -r line; \
    do printf "%s %s\n" \
      $(echo "$line" | awk -F'user=' '{print $2}' | awk -F'|' '{print $1}') \
      $(echo "$line" | awk -F'source_ip=' '{print $2}' | awk -F'|' '{print $1}'); \
  done | \
  sort | uniq | wc -l) -ge 50 ]];
then
  alert.sh "Warning! Total user login failed great than 50"
fi
```

---
<br />
<br />
<br />

## :bookmark:  **Search rare run command from the user within 4 weeks, if this appear less than 2**

Capture user behaviour every minute

```bash
tail parsedlog.dat | grep -i -v "user=|" | grep -i -v "cmd=|" | \
while read -r line; do \
  printf "%s cmd=%s\n" \
    $(echo "$line" | awk -F'user=' '{print $2}' | awk -F'|' '{print $1}') \
    $(echo "$line" | awk -F'cmd=' '{print $2}' | awk -F'|' '{print $1}'); \
done >> useractivity-$(date +%Y%m%d%H%M).dat
```

:page_facing_up: `useractivity-202507021154.dat`\
:page_facing_up: `useractivity-202507021155.dat`\
:page_facing_up: `useractivity-202507021156.dat`\
:page_facing_up: `useractivity-202507021157.dat`\
:page_facing_up: `useractivity-202507021158.dat`\
:page_facing_up: `useractivity-202507021159.dat`

> michael.dell cmd=outlook.exe\
> sundar.pichai cmd=ping.exe\
> john.stankey cmd=msteams.exe\
> eddie.chu cmd=ADExplorer64.exe\
> john.stankey cmd=excel.exe


Search against captured user behaviour, see how many times appear in the past

```bash
tail parsedlog.dat | grep -i -v "user=|" | grep -i -v "cmd=|" | \
while read -r line; do \
  keyword1=$(printf "%s" \
    $(echo "$line" | awk -F'user=' '{print $2}' | awk -F'|' '{print $1}')); \
  keyword2=$(printf "cmd=%s" \
    $(echo "$line" | awk -F'cmd=' '{print $2}' | awk -F'|' '{print $1}')); \
  find useractivity-*.dat -maxdepth 1 -mtime -28 | xargs -P 0 -n 1 grep -i -H "$keyword1" | grep -i "$keyword2"; \
done | wc -l
```
`xargs -P 0 ...` run in multiple processes utilize all processors

---
<br />
<br />
<br />

## :bookmark:  **Search rare lateral connection from the user within 4 weeks, if this appear less than 2**

Capture user behaviour every minute

```bash
tail parsedlog.dat | \
grep -i "sysmon" | grep -i "=10.\|=172.16.\|=172.17.\|=172.18.\|=172.19.\|=172.20.\|=172.21.\|=172.22.\|=172.23.\|=172.24.\|=172.25.\|=172.26.\|=172.27.\|=172.28.\|=172.29.\|=172.30.\|=172.31.\|=192.168.\|=127.\|=169.254." | \
while read -r line; do \
  printf "%s target_ip=%s\n" \
    $(echo "$line" | awk -F'user=' '{print $2}' | awk -F'|' '{print $1}') \
    $(echo "$line" | awk -F'target_ip=' '{print $2}' | awk -F'|' '{print $1}'); \
done >> useractivity-$(date +%Y%m%d%H%M).dat
```

:page_facing_up: `useractivity-202507021154.dat`\
:page_facing_up: `useractivity-202507021155.dat`\
:page_facing_up: `useractivity-202507021156.dat`\
:page_facing_up: `useractivity-202507021157.dat`\
:page_facing_up: `useractivity-202507021158.dat`\
:page_facing_up: `useractivity-202507021159.dat`

> michael.dell target_ip=10.20.100.23\
> sundar.pichai target_ip=10.20.100.77\
> john.stankey target_ip=10.30.100.32\
> eddie.chu target_ip=10.20.200.123\
> john.stankey target_ip=10.30.100.202


Search against captured user behaviour, see how many times appear in the past

```bash
tail parsedlog.dat | \
grep -i "sysmon" | grep -i "=10.\|=172.16.\|=172.17.\|=172.18.\|=172.19.\|=172.20.\|=172.21.\|=172.22.\|=172.23.\|=172.24.\|=172.25.\|=172.26.\|=172.27.\|=172.28.\|=172.29.\|=172.30.\|=172.31.\|=192.168.\|=127.\|=169.254." | \
while read -r line; do \
  keyword1=$(printf "%s" \
    $(echo "$line" | awk -F'user=' '{print $2}' | awk -F'|' '{print $1}')); \
  keyword2=$(printf "target_ip=%s" \
    $(echo "$line" | awk -F'target_ip=' '{print $2}' | awk -F'|' '{print $1}')); \
  find useractivity-*.dat -maxdepth 1 -mtime -28 | xargs -P 0 -n 1 grep -i -H "$keyword1" | grep -i "$keyword2"; \
done | wc -l
```
`xargs -P 0 ...` run in multiple processes utilize all processors

You can have user, process, target IP and more combination tracking


---
<br />
<br />
<br />

## :bookmark:  **Search abnormal upload from the user in past 24 hours, if it is more than 100MB**

Capture user behaviour every minute

```bash
tail parsedlog.dat | grep -i -v "user=|" | grep -i -v "target_fqdn=|" | grep -i -v "sent_byte=|" | \
while read -r line; do \
  printf "%s %s sent_byte=%s\n" \
    $(echo "$line" | awk -F'user=' '{print $2}' | awk -F'|' '{print $1}') \
    $(echo "$line" | awk -F'target_fqdn=' '{print $2}' | awk -F'|' '{print $1}') \
    $(echo "$line" | awk -F'sent_byte=' '{print $2}' | awk -F'|' '{print $1}'); \
done >> useractivity-$(date +%Y%m%d%H%M).dat
```

:page_facing_up: `useractivity-202507021154.dat`\
:page_facing_up: `useractivity-202507021155.dat`\
:page_facing_up: `useractivity-202507021156.dat`\
:page_facing_up: `useractivity-202507021157.dat`\
:page_facing_up: `useractivity-202507021158.dat`\
:page_facing_up: `useractivity-202507021159.dat`

> michael.dell drive.google.com sent_byte=739347\
> sundar.pichai us.yahoo.com sent_byte=98739\
> john.stankey google.com sent_byte=23023298\
> eddie.chu www.amazon.com sent_byte=203948\
> michael.dell drive.google.com sent_byte=34082734\
> john.stankey www.cnn.com sent_byte=137


Search against captured user behaviour, see total upload size per site and user.

```bash
find useractivity-*.dat -maxdepth 1 -mtime -1 -print0 | xargs -P 0 -0 \
  awk -F'sent_byte=' '{sum[$1] += $2} END {for (user in sum) if (sum[user] > 104857600) print user sum[user]}'
```
`xargs -P 0 ...` run in multiple processes utilize all processors


---
<br />
<br />
<br />

## :bookmark:  **Search abnormal session from the user in past 24 hours, if it is more than 12 hours, suspicious command and control (C2) conneciton**

Capture user behaviour every minute

```bash
tail parsedlog.dat | grep -i -v "user=|" | grep -i -v "target_fqdn=|" | grep -i -v "session_duration=|" | \
while read -r line; do \
  printf "%s %s session_duration=%s\n" \
    $(echo "$line" | awk -F'user=' '{print $2}' | awk -F'|' '{print $1}') \
    $(echo "$line" | awk -F'target_fqdn=' '{print $2}' | awk -F'|' '{print $1}') \
    $(echo "$line" | awk -F'session_duration=' '{print $2}' | awk -F'|' '{print $1}'); \
done >> useractivity-$(date +%Y%m%d%H%M).dat
```

:page_facing_up: `useractivity-202507021154.dat`\
:page_facing_up: `useractivity-202507021155.dat`\
:page_facing_up: `useractivity-202507021156.dat`\
:page_facing_up: `useractivity-202507021157.dat`\
:page_facing_up: `useractivity-202507021158.dat`\
:page_facing_up: `useractivity-202507021159.dat`

> michael.dell drive.google.com session_duration=7934\
> sundar.pichai us.yahoo.com session_duration=9739\
> john.stankey nonstoptogo.io session_duration=57039\
> eddie.chu www.amazon.com session_duration=248\
> michael.dell mysharepoint.com session_duration=3734\
> john.stankey www.cnn.com session_duration=137


Search against captured user behaviour, see how long user connect to them, excluding legitimate long connection website, e.g. web.whatsapp.com

```bash
find useractivity-*.dat -maxdepth 1 -mtime -1 | xargs -P 0 -n 1 \
  grep -i -v "sharepoint.com\|outlook.com\|gmail.com\|web.whatsapp.com" \
  awk -F'session_duration=' '{sum[$1] += $2} END {for (user in sum) if (sum[user] > 43200) print user sum[user]}'
```
`xargs -P 0 ...` run in multiple processes utilize all processors

<br />
<br />
<br />

<!-- 
-->
