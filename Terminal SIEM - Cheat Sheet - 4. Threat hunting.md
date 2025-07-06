# **Terminal SIEM - Cheat Sheet - Threat hunting**

## :bookmark:  **Search threat patterns form the syslog or parsed log**

```bash
tail rsyslog.log | xargs -P 0 -I {} sh -c 'echo "{}" | grep -i "mimikatz"'; done
tail parsedlog.dat | xargs -P 0 -I {} sh -c 'echo "{}" | grep -i "log_type:firewall" | grep -i "source_ip:192.168.21.37"'; done
```
`xargs -P 0 ...` run in multiple processes utilize all processors

OR
```bash
tail rsyslog.log | parallel -j 0 --pipe grep -i "mimikatz"
tail parsedlog.dat | parallel -j 0 --pipe grep -E "log_type:firewall.*source_ip:192.168.21.37"
```
`parallel -j 0 ...` run in multiple processes utilize all processors

---

 \
2\
 

## :bookmark:  **Search threat patterns within date range**

```bash
find rsyslog*.log -type f -newermt "2045-05-01 00:00:00" \! -newermt "2045-05-02 00:00:00" | xargs -P 0 -n 1 grep -i "mimikatz"
find parsedlog*.dat -type f -newermt "2045-05-01 00:00:00" \! -newermt "2045-05-02 00:00:00" | xargs -P 0 -n 1 grep -i "log_type:firewall" | grep -i "source_ip:192.168.21.37"
```
`xargs -P 0 ...` run in multiple processes utilize all processors

OR
```bash
find rsyslog*.log -type f -newermt "2045-05-01 00:00:00" \! -newermt "2045-05-02 00:00:00" | parallel -j 0 grep -i "mimikatz"
find parsedlog*.dat -type f -newermt "2045-05-01 00:00:00" \! -newermt "2045-05-02 00:00:00" | parallel -j 0 grep -E "log_type:firewall.*source_ip:192.168.21.37"
```
`parallel -j 0 ...` run in multiple processes utilize all processors

<!-- https://www.gnu.org/software/parallel/parallel_examples.html#example-parallel-grep
-->
