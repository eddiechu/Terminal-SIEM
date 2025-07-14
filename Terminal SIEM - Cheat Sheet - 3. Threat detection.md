# **Terminal SIEM - Cheat Sheet - Threat detection**

## :bookmark:  **Search threat patterns in batch from parsed log**
:page_facing_up: `detection.sh`
```bash
#!/bin/bash
while IFS= read -r line; do
  if [ "$line" =~ "log_type=windows" ] && [ "$line" =~ "file=mimikatz" ]; then
    echo "Windows malware, mimikatz found!"
    echo $line
  fi
  if [ "$line" =~ "log_type=linux" ] && [ "$line" =~ "file=rm" ] && [ "$line" =~ ".bash_history" ]; then
    echo "Linux suspious activity found!"
    echo $line
  fi
done
```
```bash
tail parsedlog.dat | xargs -P 0 -I {} sh -c 'echo "{}" | detection.sh'
```
`xargs -P 0 ...` run in multiple processes utilize all processors

OR
```bash
tail parsedlog.dat | parallel -j 0 --pipe detection.sh'
``` 
`parallel -j 0 ...` run in multiple processes utilize all processors

---
<br />
<br />
<br />

## :bookmark:  **Search against cyber threat intelligence feed**
Indicators of compromise (IoCs), such as malicious URLs, IP addresses, and file hashes

:page_facing_up: `detection.sh`
```bash
#!/bin/bash
while IFS= read -r line; do
  echo "$line" | xargs -P 0 -I {} sh -c 'echo "{}" | grep -w -F -f baddomain.txt' >> ctiresult.txt
  echo "$line" | xargs -P 0 -I {} sh -c 'echo "{}" | grep -w -F -f badip.txt' >> ctiresult.txt
  echo "$line" | xargs -P 0 -I {} sh -c 'echo "{}" | grep -w -F -f badfile.txt' >> ctiresult.txt
done
```

the matched log entry append in ctiresult.txt for alert.

```bash
tail parsedlog.dat | xargs -P 0 -I {} sh -c 'echo "{}" | detection.sh'
```
`xargs -P 0 ...` run in multiple processes utilize all processors

OR
```bash
tail parsedlog.dat | parallel -j 0 --pipe detection.sh'
``` 
`parallel -j 0 ...` run in multiple processes utilize all processors

---
<br />
<br />
<br />
