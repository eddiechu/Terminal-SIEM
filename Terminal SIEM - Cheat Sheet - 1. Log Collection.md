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
