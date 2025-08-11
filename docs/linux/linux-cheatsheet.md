# Linux Commands

This document lists some of the most commonly used Linux commands by developers, with descriptions and usage examples.

---

## watch

Runs a command repeatedly, showing its output and errors. Useful for monitoring changes in real time.

Example:  
`watch ls -l`

`watch -n 5 ls -l`

---

## grep

Searches for a pattern within files or input.

Example:  
`grep "error" /var/log/syslog`

---

## tail

Shows the last part of a file.

Example:  
`tail -f /var/log/syslog`

---

## ps

Displays information about running processes.

Example:  
`ps -u $(whoami)`

---

## top

Interactive process viewer showing system resource usage.

Example:  
`top`

---

## htop

An enhanced, interactive process viewer (must be installed separately).

Example:  
`htop`

---

## chmod

Changes file permissions.

Example:  
`chmod +x script.sh`

---

## chown

Changes file owner and group.

Example:  
`chown user:group file.txt`

---

## ssh

Secure shell for logging into a remote machine.

Example:  
`ssh user@host`

---

## scp

Securely copies files between hosts.

Example:  
`scp file.txt user@remote:/path/to/destination`

---

## rsync

Efficiently syncs files/directories between locations.

Example:  
`rsync -avz /local/dir user@remote:/remote/dir`

---

## df

Shows disk space usage.

Example:  
`df -h`

---

## du

Shows disk usage of files and directories.

Example:  
`du -h --max-depth=1`

---

## find

Searches for files and directories matching criteria.

Example:  
`find . -name "*.log"`

---

## sed

Stream editor for filtering and transforming text.

Example:  
`sed 's/old/new/g' file.txt`

---

## awk

Powerful text processing and pattern scanning language.

Example:  
`awk '{print $1}' file.txt`

---

## tar

Archives and extracts files.

Example:  
Create archive:  
`tar -cvf archive.tar folder/`  
Extract archive:  
`tar -xvf archive.tar`

---

## zip / unzip

Compress and decompress files.

Example:  
`zip archive.zip file1 file2`  
`unzip archive.zip`

---

## systemctl

Controls systemd system and service manager.

Example:  
`systemctl status nginx`  
`systemctl restart nginx`

---

## journalctl

Views systemd logs.

Example:  
`journalctl -u nginx.service`

---

## uname

Displays system information.

Example:  
`uname -a`

---

## whoami

Shows current logged in user.

Example:  
`whoami`

---

## history

Shows command history.

Example:  
`history`

---

## clear

Clears the terminal screen.

Example:  
`clear`

---

## env

Shows environment variables.

Example:  
`env`

---

## export

Sets environment variables.

Example:  
`export PATH=$PATH:/new/path`

---

## alias

Creates command shortcuts.

Example:  
`alias ll='ls -la'`

---

## kill

Terminates processes by PID.

Example:  
`kill 1234`

---

## pkill

Kills processes by name.

Example:  
`pkill nginx`

---

## sleep

Pauses execution for specified seconds.

Example:  
`sleep 5`

---

## date

Shows or sets the system date/time.

Example:  
`date`

---

## printenv

Displays the current environment variables.

Example:  
`printenv`

---

## pwd

`pwd` (Print Working Directory) displays the current directory you are in within the terminal.

Example:  
`pwd`

---

# Notes

- Most commands support many options and flags; check their man pages (e.g., `man grep`) for details.
- Some tools like `htop` or `rsync` may require installation depending on your Linux distro.
