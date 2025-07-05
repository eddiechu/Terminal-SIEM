# **Terminal SIEM - Cheat Sheet - Threat detection**

## :bookmark:  **Search threat keyword form the syslog or parsed log**
```bash
tail rsyslog.log | xargs -P 0 -I {} sh -c 'echo "{}" | grep -i "mimikatz"'; done
tail parsed.db | xargs -P 0 -I {} sh -c 'echo "{}" | grep -i "source_ip:192.168.21.37"'; done
```
`xargs -P 0 ...` run in multiple processes utilize all processors

OR
```bash
tail rsyslog.log | parallel -j 0 --pipe grep -i "mimikatz"'
tail parsed.db | parallel -j 0 --pipe grep -i "source_ip:192.168.21.37"'
```
`parallel -j 0 ...` run in multiple processes utilize all processors

> [!TIP]
> Multiple searches in one batch

`detection.sh`
```bash
#!/bin/bash
while IFS= read -r line; do
  echo "$line" | grep -i "mimikatz"
  echo "$line" | grep -i "rm" | grep -i ".bash_history"
done
```
```bash
tail rsyslog.log | xargs -P 0 -I {} sh -c 'echo "{}" | detection.sh'
```
OR
```bash
tail rsyslog.log | parallel -j 0 --pipe detection.sh'
``` 
