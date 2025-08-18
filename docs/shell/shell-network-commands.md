# Shell Network Commands

This document provides an overview of commonly used shell network commands for developers, along with descriptions and
practical usage examples.

---

## hostname

Shows or sets the system's hostname.

Usage:
`hostname`

---

## ping

Checks if a hostname resolves to an IP and if the host is reachable. Sends ICMP echo requests to test network
connectivity to a host.

Usage:
`ping google.com`

➡️ Shows whether `google.com` points to the expected IP.

---

## dig

DNS lookup utility, more flexible than `nslookup`. Queries DNS resolution for a hostname.
This command is very useful for debugging DNS issues and checking DNS propagation.

Usage:
`dig example.com`

`dig example.com @111.11.11.11` # Querying a specific DNS server

---

## nslookup

Queries DNS servers to obtain domain name or IP address mapping. Alternative to dig for DNS lookup.

Usage:
`nslookup example.com`

---

## telnet

Tests if a specific hostname and port are reachable.

Usage:
`telnet example.com 8080`

---

## ifconfig

Displays or configures network interfaces (deprecated in favor of `ip` command).

Usage:
`ifconfig`

---

## curl

Transfers data from or to a server, supporting multiple protocols.

Usage:
`curl https://example.com`

---

## wget

Non-interactive network downloader, useful for downloading files from the web.

Usage:
`wget https://example.com/file.zip`

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

