# Shell scripting

### File management

* Distinguish file path
  * Just `path` is a file or directory named `path` in the current directory.
  * `./path` is a file or directory named `path` in the current directory, with the directory spelled out. The dot directory `.` represents the current directory, and `path` is the name of the file or directory within this directory.
  * `~/path` is a shorthand for `$HOME/path` where `$HOME` is a variable which refers to your home directory. Typically your home directory will be somewhere like `/home/you` or `/Users/you` where `you` is your account name. (The command `echo "$HOME"` will display your home directory.) The expanded value is an absolute path (unless you have messed up the value of `$HOME` thoroughly), as indicated by the initial slash.
  * `/path` is an absolute path which refers to a file or directory named `path` which is in the _root_ directory `/`. Every file on Unix is ultimately somewhere in the directory tree which starts with the root directory
* ls, ls -l, ls -a
* cp, mv
* symlinks
* `grep [options] pattern [files]`: pattern -> regex pattern (`^` start of a line, `$` end of a line)
  * case sensitive search: `grep -i "Unix" file.txt`
  * display the number of matches: `grep -c "unix" file.txt`
  * display the file names that matches the pattern: `grep -l "unix" f1 f2 f3 f4`
  * display the matched pattern" `grep -o "unix" file`
  * show the line number while displaying the output: `grep -n "unix" file`
* find, wc
* cut, cat, sort

### I/O redirection

* file descriptor: stdin -> 0, stdout -> 1, stderr -> 2
* `>` : write or rewrite frim left to right
* `>>`: append stdout into stdin
* `2> /dev/null`
* `&>`: both stdout and stderr
* `<` : redirect to stdin
*   `<<` : multiple line stdin redirection

    ```bash
    cat <<END > output.txt
    ```
* `{stdout} | {stdin}`: piping

### Process & System resource management

* `firefox &`: start a program
* `pidof firefox`: check pids of a program
* `ps -p <pid>` : detail info about a pid
* `ps -ef | grep firefox` : find all firefox related processes
* `kill -l`: list all signals
* `kill -9 <pid>`: kill a process with SIGKILL(9) or SIGTERM(15)
* `pstree [option] [pid or username]` : recursive tree view of processes
* `top`
  * filter by process:`o` -> `COMMAND=firefox`
  * go back to normal panel: `=`
  * kill a process: `k` -> `<pid>`
  * change updating speed: `s`
  * sort by memory consuming rate: `shift + m`
  * sort by CPU consuming rate: `shift + p`
  * quit: `q`
  * load average
  * In `top` manpage, what are `free`, `used`, `buff`? ([link](https://unix.stackexchange.com/questions/390518/what-do-the-buff-cache-and-avail-mem-fields-in-top-mean))
* `free -h`: check available and used RAM
  * `total`: RAM
  * `used`: not all used memory shown here
  * `free`: free memory, usually should be small since free means wasted
  * `available`: used to start new applications
  * `buff/cache` : buffer (data being written) + cache (data been read)

### Networking basics

* `ssh username@<ip>`
* `ifconfig`: display assigned IP address
*   `ping <ip/domain name>`

    The ping command sends a request to destination IP or domain name and wait for the reply if request is received by other party it will respond back with ICMP (Internet Control Message Protocol) echo reply.

    The `ping` command will continue to send ICMP packages to the Destination IP address until it receives an interrupt. To stop the command, just hit the `Ctrl+C` key combination.

    E.g. A way to check DNS problems is to ping domain name and ping its IP address separately. If there is no problem, two ping processes will work well. Otherwise, DNS has problems.
* `route -n` : kernel IP routing table
* `netstat`: powerful tool for network states
* `host <domain name>` : DNS lookup operations
