# **Terminal SIEM - Cheat Sheet - Threat detection**

## :bookmark:  **Search threat patterns form parsed log**
`detection.sh`
```bash
#!/bin/bash
while IFS= read -r line; do
  if [ "$line" =~ "log_type:windows" ] && [ "$line" =~ "file:mimikatz" ]; then
    echo "Windows malware, mimikatz found!"
  fi
  if [ "$line" =~ "log_type:linux" ] && [ "$line" =~ "file:rm" ] && [ "$line" =~ ".bash_history" ]; then
    echo "Linux suspious activity found!"
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
