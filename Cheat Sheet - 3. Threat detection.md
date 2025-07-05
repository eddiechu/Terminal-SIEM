# **Terminal SIEM - Cheat Sheet - Threat detection**

## :bookmark:  **Search threat keyword form the log since last check**
```bash
for f in `find \var\log\rsyslog\rsyslog*.log -mmin -3`; do pos=$(cat ${f}.lastpos); lastpos=$(stat -c %s ${f}); echo $lastpos > ${f}.lastpos; tail -c +$pos ${f} | xargs -P 0 -I {} sh -c 'echo "{}" | grep -i "mimikatz"'; done
```
OR
```bash
for f in `find \var\log\rsyslog\rsyslog*.log -mmin -3`;
do pos=$(cat ${f}.lastpos); 
  lastpos=$(stat -c %s ${f}); 
  echo $lastpos > ${f}.lastpos;

  tail -c +$pos ${f} | xargs -P 0 -I {} sh -c 'echo "{}" | grep -i "mimikatz"'; 
done
```
`find ... -mmin -3` find file last modified within 3 minutes\
`stat -c %s ...` total characters in the file\
`echo $lastpost ...` store the total characters as last position in the .pos file\
`tail -c +$pos ...` get the content after number of characters \/ since last check\
`xargs -P 0 ...` run in multiple processes\
`grep -i ...` search text, case in-sensitive

> This way, you can run it by schedule, keep tracking last check position, no duplication happen

> [!TIP]
> To archive `tail` | `grep` in multiple processes, either

```bash
tail rsyslog.log | xargs -P 0 -I {} sh -c 'echo "{}" | grep -i "mimikatz"'
```
OR
```bash
tail rsyslog.log | parallel -j 0 --pipe grep -i "mimikatz"'
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
