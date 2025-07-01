# **Terminal SIEM**
> [!NOTE]
> under construction ...\
> help people without SIEM or with SIEM but limited function.

## **Characteristic**
Attribute | Termina SIEM | Community \/ Brand SIEM
--- | --- | ---
Technology | Linux terminal, file based | Web based, index and NoSQL
Production architecture | ONE host | Several hosts per role
Sizing | xlarge | 4xlarge per host
Log injection | Hundreds thousand entries / sec | Thousand, need MQ to support more volume to minimize log loss burst
Multiprocessing | Yes | Yes
Correlation | Yes | Yes
Log parsing | by awk, jq, Golang, build from scratch, with the help of Gen AI<br/>(Can avoid regular experssion for hundreds times performance gain) | built-in parser for common log source
Detection | by grep or awk, build from scratch, convert from community threat detection rules with the help of Gen AI | Primitive, need to build most
High availability | Load balancers, 2 nodes, share storage | Can extend to HA
Scalability | Vertical | Horizontal
Dashboard and report | No | Yes
Access control | Linux | Product built-in
Flexibility | Can develop search criteria what ever you think, and with the help of Gen AI, <br/>e.g. <ul><li>any source IP to a destinated IP access more than 20 different ports</li><li>any user login O365 cross more than one countries within an hour</li><li>over 80 unique user login failed over an hour</li><li>what happen on that laptop and firewall in 3 minutes before the incident</li><li>any user to a single web site, accumulated upload size more than 100MB within a day</li><li>accumulate web access time per host per desgination within a day to spot potential C2</li></ul> | Limit to the product capability
Patch / security management | Linux | Linux and product
Store raw log | Yes, raw log and parsed log | No
Retention | File management | Index management
Skill set required | Linux rsyslog, grep, awk, jq, find, sort, uniq, file management, etc (may need Golang and Python for advanced usage)| Product knowledge

## **Cheat Sheet**

## Log Collection
:bookmark:  **example 1**

> sample log content

``` 
command line
```
`-r` parameter 1\
`-t` parameter 2
> result 1\
> result 2

## Log Parsing
:bookmark:  **example 1**

> sample log content

``` 
command line
```
`-r` parameter 1\
`-t` parameter 2
> result 1\
> result 2

## Threat hunting \/ detection
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
