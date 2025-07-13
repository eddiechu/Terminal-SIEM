# **Terminal SIEM**
> [!NOTE]
> under construction ...\
> Help administrator without SIEM or with SIEM but don't have coomplete telemetry for searching becase of license cost, with SIEM but limited search function

## **Characteristics**
Attribute | Termina SIEM | Community \/ Brand SIEM
--- | --- | --- 
Strength | **Super light, because of no indexing, database, GUI, and minimal the use of regular expression<br /><br />Super fast, parallel processing<br /><br />Unlimited search idea, develop what ever search criteria you think** | GUI
Technology | **Linux terminal, file based** | Web based, index and NoSQL
Production architecture | **ONE host** | Several hosts per role
Sizing | **xlarge** | 4xlarge per host
Log injection | **Hundreds thousand entries / sec** | Thousands entries / sec, need MQ to support more volume to minimize log loss burst
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
