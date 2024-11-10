# Crontab

The basic crontab syntax consists of 5 time fields plus the command to execute:

```
* * * * * command_to_execute
│ │ │ │ │
│ │ │ │ └─── Day of the week (0-6) (Sunday = 0)
│ │ │ └────── Month (1-12)
│ │ └───────── Day of the month (1-31)
│ └──────────── Hour (0-23)
└────────────── Minute (0-59)
```

#### 1. Special Characters:

```
*    : Any value/always
,    : Value list separator
-    : Range of values
/    : Step values
```

#### 2. Common Examples:

```bash
# Every minute
* * * * * command

# Every hour (at minute 0)
0 * * * * command

# Every day at 2am
0 2 * * * command

# Every Monday at 3am
0 3 * * 1 command

# Every weekday (Monday to Friday) at 6am
0 6 * * 1-5 command

# Every 6 hours (at 00:00, 06:00, 12:00, 18:00)
0 */6 * * * command

# Multiple times: At 3am and 6pm
0 3,18 * * * command

# Every 30 minutes
*/30 * * * * command

# First day of every month at midnight
0 0 1 * * command

# Every Saturday and Sunday at 8am
0 8 * * 6,0 command
```

#### 3. Special Keywords:

```bash
@yearly   (or @annually) : Run once a year at midnight on January 1st (0 0 1 1 *)
@monthly  : Run once a month at midnight on the first (0 0 1 * *)
@weekly   : Run once a week at midnight on Sunday (0 0 * * 0)
@daily    : Run once a day at midnight (0 0 * * *)
@hourly   : Run once an hour at the beginning of the hour (0 * * * *)
@reboot   : Run once at startup
```

#### 4. Practical Examples with Common Use Cases:

```bash
# Database backup every day at 1am
0 1 * * * /usr/local/bin/db_backup.sh

# Log rotation every Monday at 3am
0 3 * * 1 /usr/local/bin/rotate_logs.sh

# Website health check every 5 minutes
*/5 * * * * curl -s http://website.com/health

# Clean temp files every Sunday at midnight
0 0 * * 0 rm -rf /tmp/*

# System updates every Saturday at 4am
0 4 * * 6 apt-get update && apt-get upgrade -y

# Multiple backups at different times
0 */4 * * * /backup/hourly_backup.sh  # Every 4 hours
0 0 * * * /backup/daily_backup.sh     # Daily at midnight
0 0 * * 0 /backup/weekly_backup.sh    # Weekly on Sunday
```

#### 5. Common Operations:

```bash
# List current crontab
crontab -l

# Edit crontab
crontab -e

# Remove all cron jobs
crontab -r

# Create new crontab from file
crontab filename

# View cron logs (typical locations)
tail -f /var/log/syslog    # Debian/Ubuntu
tail -f /var/log/cron      # CentOS/RHEL
```

#### 6. Best Practices:

```bash
# 1. Always use full paths in cron jobs
0 1 * * * /usr/local/bin/script.sh

# 2. Redirect output to a log file
0 1 * * * /usr/local/bin/script.sh >> /var/log/script.log 2>&1

# 3. Include error handling in scripts
0 1 * * * /usr/local/bin/script.sh || echo "Script failed" | mail -s "Cron failed" admin@example.com

# 4. Set environment variables if needed
0 1 * * * . $HOME/.profile; /usr/local/bin/script.sh

# 5. Use lock files to prevent overlapping
0 * * * * flock -n /tmp/script.lock -c '/usr/local/bin/script.sh'
```

#### 7. Debugging Tips:

```bash
# Test your script manually first
$ ./script.sh

# Check if cron is running
$ systemctl status cron

# Check cron logs
$ grep CRON /var/log/syslog

# Make sure script is executable
$ chmod +x /path/to/script.sh

# Test with more verbose output
* * * * * /path/to/script.sh >> /var/log/script.log 2>&1
```
