# **Terminal SIEM**
> [!CAUTION]
> **under construction** ...

![Terminal SIEM! Super light, super fast, unlimited search idea](https://github.com/eddiechu/Terminal-SIEM/blob/main/image/terminalsiem1.gif?raw=true)

## **Overview**
Terminal SIEM is a lightweight, command-line-based security monitoring solution that leverages Linux's native file processing capabilities to provide enterprise-grade security information and event management (SIEM) functionality. Unlike traditional SIEM platforms that rely on databases, indexing systems, and web interfaces, Terminal SIEM operates entirely through file-based processing using standard Linux commands and automated via cron and batch jobs.

Terminal SIEM addresses three critical gaps in the security monitoring landscape:<br />
- Organizations without SIEM capabilities - Provides immediate security monitoring without significant infrastructure investment<br />
- Organizations with incomplete SIEM coverage - Offers unlimited log processing without licensing restrictions<br />
- Organizations with limited SIEM functionality - Delivers flexible search capabilities beyond product constraints<br />

<br />
<br />
<br />

## **Characteristics**
Attribute | Termina SIEM | Community \/ Brand SIEM
--- | --- | --- 
Strength | **<ins>Super light</ins> - due to no indexing, database and GUI<br /><ins>Super fast</ins> - with parallel processing and minimal regular expression usage<br /><ins>Unlimited search idea</ins> - what ever your need** | Feature-rich GUI
Technology | **Linux terminal, file-based** | Web-based, indexed NoSQL
Multiprocessing | **Yes - Linux GNU parallel or xargs** | Depends on product
Production architecture | **Single host** | Several hosts per role
Sizing | **xlarge** | 4xlarge per host
High availability | **Supported via load balancers, nodes and share storage** | Extensible to HA
Scalability | **Vertical scaling** | Horizontal scaling
Log injection | **Hundreds of thousands of log entries / sec** | Thousands of log entries / sec (requires message queue for higher volumes to minimize log loss during bursts)
Correlation | **Supported** | Supported
Log parsing | **awk or Golang, built from scratch<br/>(*Can avoid regular experssion for hundreds of times performance gain*)** | Built-in parsers for common log sources
Threat detection | **Custom-built using grep, awk or Python dataframe, support conversion of community rules, e.g. Sigma rules, with Gen AI assistance** | Primitive detection, requires further customization
Flexibility | **Develop any search criteria your need** | Limit to the product capabilities
Dashboard and report | **No** | Yes
Access control | **Managed via Linux permissions** | Product feature
Patch / security management | **Linux update** | Linux and product update
Store raw log | **Yes - stores both raw and parsed log** | No
Retention | **File management** | Index management
Immutable | **Supported with chattr and hash** | Product feature
Skill set required | **Linux rsyslog, grep, awk, file management (*may need Golang for complex parsing, Python for complex searching*)** | Product-specific knowledge

<br />
<br />
<br />

## **Design**
![Terminal SIEM! Super light, super fast, unlimited search idea](https://github.com/eddiechu/Terminal-SIEM/blob/main/image/terminalsiem2.svg?raw=true)

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
