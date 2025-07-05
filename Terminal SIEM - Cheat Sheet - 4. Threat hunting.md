# **Terminal SIEM - Cheat Sheet - Threat hunting**

## :bookmark:  **example 2**

```bash
tail rsyslog.log | xargs -P 0 -I {} sh -c 'echo "{}" | grep -i "mimikatz"'; done
tail parsedlog.dat | xargs -P 0 -I {} sh -c 'echo "{}" | grep -i "log_type:firewall" | grep -i "source_ip:192.168.21.37"'; done
```
`xargs -P 0 ...` run in multiple processes utilize all processors

OR
```bash
tail rsyslog.log | parallel -j 0 --pipe grep -i "mimikatz"'
tail parsedlog.dat | parallel -j 0 --pipe grep -E "log_type:firewall.*source_ip:192.168.21.37"'
```
`parallel -j 0 ...` run in multiple processes utilize all processors

