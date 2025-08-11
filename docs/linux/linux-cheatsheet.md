# Linux Commands

This document lists some of the most commonly used Linux commands by developers, with descriptions and usage examples.

---

## watch

Runs a command repeatedly, showing its output and errors. Useful for monitoring changes in real time.

Usage:  
`watch ls -l`

`watch -n 5 ls -l`

---

## grep

Searches for a pattern within files or input.

Usage:  
`grep "error" /var/log/syslog`

---

## tail

Shows the last part of a file.

Usage:  
`tail -f /var/log/syslog`

---

## ps

Displays information about running processes.

Usage:  
`ps -u $(whoami)`

---

## top

Interactive process viewer showing system resource usage.

Usage:  
`top`

---

## htop

An enhanced, interactive process viewer (must be installed separately).

Usage:  
`htop`

---

## chmod

Changes file permissions.

Usage:  
`chmod +x script.sh`

---

## chown

Changes file owner and group.

Usage:  
`chown user:group file.txt`

---

## ssh

Secure shell for logging into a remote machine.

Usage:  
`ssh user@host`

---

## scp

Securely copies files between hosts.

Usage:  
`scp file.txt user@remote:/path/to/destination`

---

## rsync

Efficiently syncs files/directories between locations.

Usage:  
`rsync -avz /local/dir user@remote:/remote/dir`

---

## df

Shows disk space usage.

Usage:  
`df -h`

---

## du

Shows disk usage of files and directories.

Usage:  
`du -h --max-depth=1`

---

## find

Searches for files and directories matching criteria.

Usage:  
`find . -name "*.log"`

---

## sed

Stream editor for filtering and transforming text.

Usage:  
`sed 's/old/new/g' file.txt`

---

## awk

Powerful text processing and pattern scanning language.

Usage:  
`awk '{print $1}' file.txt`

---

## tar

Archives and extracts files.

Usage:  
Create archive:  
`tar -cvf archive.tar folder/`  
Extract archive:  
`tar -xvf archive.tar`

---

## zip / unzip

Compress and decompress files.

Usage:  
`zip archive.zip file1 file2`  
`unzip archive.zip`

---

## systemctl

Controls systemd system and service manager.

Usage:  
`systemctl status nginx`  
`systemctl restart nginx`

---

## journalctl

Views systemd logs.

Usage:  
`journalctl -u nginx.service`

---

## whoami

Shows current logged in user.

Usage:  
`whoami`

---

## history

Shows command history.

Usage:  
`history`

---

## clear

Clears the terminal screen.

Usage:  
`clear`

---

## env

Shows environment variables.

Usage:  
`env`

---

## export

Sets environment variables.

Usage:  
`export PATH=$PATH:/new/path`

---

## alias

Creates command shortcuts.

Usage:  
`alias ll='ls -la'`

---

## kill

Terminates processes by PID.

Usage:  
`kill 1234`

---

## pkill

Kills processes by name.

Usage:  
`pkill nginx`

---

## sleep

Pauses execution for specified seconds.

Usage:  
`sleep 5`

---

## date

Shows or sets the system date/time.

Usage:  
`date`

---

## printenv

Displays the current environment variables.

Usage:  
`printenv`

---

## pwd

`pwd` (Print Working Directory) displays the current directory you are in within the terminal.

Usage:  
`pwd`

---

## arch

`arch` prints the architecture of the machine you are currently using.

Usage:  
`arch`

Example output:  
`x86_64` or `arm64`

---

## uname

Displays system information.

Usage:  
`uname -a`

`uname -m` # Displays machine hardware name like `x86_64` or `arm64`

---

## which

`which` shows the full path of the executable that would run when you type the command.

Usage:  
`which brew`

Example output:  
`/usr/local/bin/brew`

---

# Notes

- Most commands support many options and flags; check their man pages (e.g., `man grep`) for details.
- Some tools like `htop` or `rsync` may require installation depending on your Linux distro.
