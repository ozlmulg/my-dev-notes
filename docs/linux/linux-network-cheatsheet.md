# Linux Network Commands

This document lists some of the most commonly used Linux network commands by developers, with descriptions and usage
examples.

---

## hostname

Shows or sets the system's hostname.

Usage:
`hostname`

---

## nslookup

Queries DNS servers to obtain domain name or IP address mapping.

Usage:
`nslookup example.com`

---

## dig

DNS lookup utility, more flexible than nslookup.
This command is very useful for debugging DNS issues and checking DNS propagation.

Usage:
`dig example.com`

`dig example.com @111.11.11.11` # Querying a specific DNS server

---

## ifconfig

Displays or configures network interfaces (deprecated in favor of `ip` command).

Usage:
`ifconfig`

---

## wget

Non-interactive network downloader, useful for downloading files from the web.

Usage:
`wget https://example.com/file.zip`

---

## ping

Sends ICMP echo requests to test network connectivity to a host.

Usage:
`ping google.com`

---

## curl

Transfers data from or to a server, supporting multiple protocols.

Usage:
`curl https://example.com`

---

## ip

Modern tool to show/manipulate routing, devices, policy routing, and tunnels.

Usage:
`ip addr show`

---

## traceroute

Shows the path packets take to reach a host.

Usage:
`traceroute google.com`

---

## netstat

Displays network connections, routing tables, interface statistics, etc.

Usage:
`netstat -tuln`

---

