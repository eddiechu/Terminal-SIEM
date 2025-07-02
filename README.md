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
:bookmark:  **Consolidate syslog to single file, use timestamp as file name**

in the /etc/rsyslog.conf, configure to use timestamp as file name, e.g. rsyslog-202507020420.log

``` 
$template CustomTemplate, "/var/rsyslog/rsyslog-%$YEAR%%$MONTH%%$DAY%%$HOUR%%$MINUTE%.log"
*.* ?CustomTemplate;RSYSLOG_TraditionalFileFormat
$ActionFileDefaultTemplate RSYSLOG_TraditionalFileFormat
```
then restart rsyslog service to take effective

### <ins>Log Parsing</ins>
:bookmark:  **example 1**

> sample log content

``` 
command line
```
`-r` parameter 1\
`-t` parameter 2
> result 1\
> result 2

### <ins>Threat hunting \/ detection</ins>
:bookmark:  **example 1**

> sample log content

``` 
command line
```
`-r` parameter 1\
`-t` parameter 2
> result 1\
> result 2
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
