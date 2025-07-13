# **Terminal SIEM - Cheat Sheet - Log Collection**

## :bookmark:  **Consolidate all syslog sources to a single file, use timestamp as file name**

in the /etc/rsyslog.conf, configure to use TCP (minimize log loss) and timestamp as file name, e.g. rsyslog-204507020420.log

```
module(load="imtcp")
input(type="imtcp" port="514")

$template CustomTemplate, "/var/rsyslog/rsyslog-%$YEAR%%$MONTH%%$DAY%%$HOUR%.log"
*.* ?CustomTemplate;RSYSLOG_TraditionalFileFormat

$ActionFileDefaultTemplate RSYSLOG_TraditionalFileFormat
```
then restart rsyslog service to take effective

:page_facing_up: `rsyslog-2045070206.log`\
:page_facing_up: `rsyslog-2045070207.log`\
:page_facing_up: `rsyslog-2045070208.log`\
:page_facing_up: `rsyslog-2045070209.log`\
:page_facing_up: `rsyslog-2045070210.log`\
:page_facing_up: `rsyslog-2045070211.log`

---


---
<br />
<br />
<br />

## :bookmark:  **Pull log from SaaS, then inject to syslog**

Pull log from SaaS, convert json format to one line CSV then inject to rsyslog
```
 curl -X "GET" -H "accept: application/json" -H "Netskope-Api-Token: 41c0a35ff506d0f9673dc88bad0053a8" "https://wlb.goskope.com/api/v2/events/dataexport/events/network?operation=next" | jq --raw-output '.result[] | del(.custom_attr)| to_entries | map(.key + "=" + (.value|tostring)) | join(",")'

curl -X "GET" -H "accept: application/json" -H "Rest-Api-Token: abcdefghijklmnopqrstuvwxyz0123456789" "https://mysaas.com/api/events" | \
  jq --raw-output '.result[] | del(.custom_attr) | to_entries | map(.key + "=" + (.value|tostring)) | join(",") | \
  nc -w 0 -t 127.0.0.1 514
```
