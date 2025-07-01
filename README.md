# **Terminal SIEM**
> [!NOTE]
> under construction ...

## **Characteristic**
Function | Termina SIEM | Community \/ Brand SIEM
--- | --- | ---
Technology | Terminal, file based | Web based, index and NoSQL
Production architecture | One host | Several hosts per role
Sizing | xlarge | 4xlarge per host
Multiprocessing | Yes | Yes
Log parsing | by awk, jq, golang, build from scratch, with the help of Gen AI | Common log source
Detection | by grep or awk, build from scratch, convert from community threat detection rules with the help of Gen AI | Primitive, need to build most
High availability | Can extend to HA | Can extend to HA
Scalable | Vertical | Horizontal
Dashboard and report | No | Yes
Flexibility | Can develop what even you think | Limit to the product capability
Patch / security management | Linux | Linux and product
Store raw log | Yes, raw log and parsed log | No, parsed log
Retention | File management | Index management

## **Cheat Sheet**

## Log Parsing
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

## Threat hunting \/ Detection
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
