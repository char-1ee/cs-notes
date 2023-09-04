# SadServers

#### "Saint John": what is writing to this log file?

<pre class="language-bash"><code class="lang-bash">lsof # show a list of files are opened and which process attached to that

# Get the PID
fuser /var/log/bad.log # find the process using the file system
<strong>lsof | grep /var/log/bad.log # get the PID
</strong><strong>
</strong><strong># Kill by PID
</strong>kill -9 &#x3C;PID> # -9: SIGKILL
</code></pre>

**"Saskatoon": counting IPs**

```bash
# Get the firs field/column of the file
awk ${print $1} access.log |\

# sort and then get number of each IP's duplicates number
sort | uniq -c \

# sort ascendingly and echo
sort -r | head -1 | awk '{print $2}' > ~/highestip.txt
```

#### "Santiago": find the secret combination

```sh
# Find the #lines where target string accurs
find ./ -type f -name "*.txt" | xargs grep -c "Alice"
```
