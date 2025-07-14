# **Terminal SIEM**
> [!CAUTION]
> **under construction** ...

[![Terminal SIEM](https://github.com/eddiechu/Terminal-SIEM/blob/main/image/terminalsiem1.gif?raw=true)]([https://linuxserver.aio](https://github.com/eddiechu/Terminal-SIEM/))

Share with administrator<br />without SIEM, <br />with SIEM but don't have coomplete telemetry for searching (becase of license cost) or <br />with SIEM but have limited search function

## **Characteristics**
Attribute | Termina SIEM | Community \/ Brand SIEM
--- | --- | --- 
Strength | **<ins>Super light</ins>, because of no indexing, database and GUI<br /><ins>Super fast</ins>, parallel processing and minimal the use of regular expression<br /><ins>Unlimited search idea</ins>, what ever your want** | GUI
Technology | **Linux terminal, file based** | Web based, index and NoSQL
Multiprocessing | **Yes, with Linux GNU parallel or xargs** | Depends
Production architecture | **Single host** | Several hosts per role
Sizing | **xlarge** | 4xlarge per host
High availability | **Load balancers, nodes and share storage** | Can extend to HA
Scalability | **Vertical** | Horizontal
Log injection | **Hundreds thousand log entries / sec** | Thousands log entries / sec, need message queue to support more volume to minimize log loss burst
Correlation | **Yes** | Yes
Log parsing | **by awk or Golang, build from scratch<br/>(*Can avoid regular experssion for hundreds times performance gain*)** | built-in parser for common log source
Threat detection | **by grep, awk or Python dataframe, build from scratch, or convert from community rules with the help of Gen AI** | Primitive, need to further build
Flexibility | **Can develop search criteria what ever you think** | Limit to the product capability
Dashboard and report | **No** | Yes
Access control | **Linux** | Product feature
Patch / security management | **Linux** | Linux and product
Store raw log | **Yes, raw log and parsed log** | No
Retention | **File management** | Index management
Immutable | **Support with chattr and hash** | Yes
Skill set required | **Linux rsyslog, grep, awk, file management, etc (*may need Golang for advanced parsing, Python for advanced searching*)** | Product knowledge

<br />
<br />
<br />

## **Cheat Sheet**
Start from here, then go to more adaptable and advanced with the help of your Gen AI buddies

Cheat Sheet [Log Collection](Terminal%20SIEM%20-%20Cheat%20Sheet%20-%201.%20Log%20Collection.md)

Cheat Sheet [Log Parsing](Terminal%20SIEM%20-%20Cheat%20Sheet%20-%202.%20Log%20Parsing.md)

Cheat Sheet [Threat detection](Terminal%20SIEM%20-%20Cheat%20Sheet%20-%203.%20Threat%20detection.md)

Cheat Sheet [Threat hunting](Terminal%20SIEM%20-%20Cheat%20Sheet%20-%204.%20Threat%20hunting.md)


#
#tag
siem
Security information and event management
blue team
soc
red team
opensearch
elasticsearch
search
index
security
nosql
syslog
rsyslog
collection
parsing
threat detection
threat hunting
