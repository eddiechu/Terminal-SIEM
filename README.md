# **Terminal SIEM**
> [!CAUTION]
> **under construction** ...

![Terminal SIEM! Super light, super fast, unlimited search idea](https://github.com/eddiechu/Terminal-SIEM/blob/main/image/terminalsiem1.gif?raw=true)

Share with administrator<br />
- without SIEM <br />
- with SIEM but don't have coomplete telemetry for searching (becase of license cost) <br />
- with SIEM but have limited search function

## **Characteristics**
Attribute | Termina SIEM | Community \/ Brand SIEM
--- | --- | --- 
Strength | **<ins>Super light</ins> - due to no indexing, database and GUI<br /><ins>Super fast</ins> - with parallel processing and minimal regular expression usage<br /><ins>Unlimited search idea</ins> - what ever your need** | GUI
Technology | **Linux terminal, file-based** | Web-based, indexed NoSQL
Multiprocessing | **Yes - Linux GNU parallel or xargs** | Depends on product
Production architecture | **Single host** | Several hosts per role
Sizing | **xlarge** | 4xlarge per host
High availability | **Load balancers, nodes and share storage** | Can extend to HA
Scalability | **Vertical** | Horizontal
Log injection | **Hundreds of thousands of log entries / sec** | Thousands of log entries / sec (requires message queue for higher volumes to minimize burst log loss)
Correlation | **Yes** | Yes
Log parsing | **awk or Golang, built from scratch<br/>(*Can avoid regular experssion for hundreds of times performance gain*)** | Built-in parsers for common log sources
Threat detection | **grep, awk or Python dataframe, built from scratch, can convert community rules, e.g. Sigma rules, with Gen AI assistance** | Primitive capabilities, requires additonal setup
Flexibility | **Develop any search criteria your need** | Limit to the product capabilities
Dashboard and report | **No** | Yes
Access control | **Linux-based** | Product feature
Patch / security management | **Linux** | Linux and product
Store raw log | **Yes - stores both raw and parsed log** | No
Retention | **File management** | Index management
Immutable | **Supported with chattr and hash** | Yes
Skill set required | **Linux rsyslog, grep, awk, file management (*may need Golang for complex parsing, Python for complex searching*)** | Product-specific knowledge

<br />
<br />
<br />

## **Design**
![Terminal SIEM! Super light, super fast, unlimited search idea](https://github.com/eddiechu/Terminal-SIEM/blob/main/image/terminalsiemdiagram2.svg?raw=true)

<br />
<br />
<br />

## **Cheat Sheet**
Begin with these foundational resources, then advance to more sophisticated implementations with assistance from Gen AI tools:

Cheat Sheet - [Log Collection](Terminal%20SIEM%20-%20Cheat%20Sheet%20-%201.%20Log%20Collection.md)

Cheat Sheet - [Log Parsing](Terminal%20SIEM%20-%20Cheat%20Sheet%20-%202.%20Log%20Parsing.md)

Cheat Sheet - [Threat detection](Terminal%20SIEM%20-%20Cheat%20Sheet%20-%203.%20Threat%20detection.md)

Cheat Sheet - [Threat hunting](Terminal%20SIEM%20-%20Cheat%20Sheet%20-%204.%20Threat%20hunting.md)


#
#tag
siem
Security information and event management
blue team
soc
soc analyst
red team
opensearch
elasticsearch
elk
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
log server
Sigma rules
splunk
qradar
sentinel
