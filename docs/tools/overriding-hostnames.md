# Overriding Hostnames

When testing services on private or temporary servers, there may not be public DNS pointing to the server IPs. You can
override hostnames on macOS using the **`/etc/hosts` file** or temporarily with `curl --resolve`.

---

## 1. macOS Hosts File

The hosts file is located at:

`/etc/hosts`

You can edit it with sudo privileges:

```bash
sudo nano /etc/hosts # use nano

sudo subl /etc/hosts # or use your preferred text editor
```

### Example Entries

**Add your custom overrides (`IP -> HOST` pairs) inside `/etc/hosts` file:**

```
##
# Host Database
#
# localhost is used to configure the loopback interface
# when the system is booting.  Do not change this entry.
##
127.0.0.1	localhost
255.255.255.255	broadcasthost
::1             localhost
# Added by Docker Desktop
# To allow the same kube context to work on the host and the container:
127.0.0.1 kubernetes.docker.internal
# End of section

# Custom overrides
192.168.1.100 app.example.com
192.168.1.100 api.example.com
192.168.1.100 dashboard.example.com
```

> **Note:** Multiple hostnames can point to the same IP. This is useful when a server hosts multiple services.

When you ping or accessing `app.example.com`, it will resolve to `192.168.1.100`:

```bash
$ ping app.example.com
PING app.example.com (192.168.1.100): 56 data bytes
Request timeout for icmp_seq 0
...
```

---

## 2. Testing with `curl`

Instead of editing the hosts file, you can temporarily map a hostname to an IP using the `--resolve` option in `curl`:

```bash
curl --resolve app.example.com:443:192.168.1.100 -o /dev/null -v https://app.example.com
```

Replace the hostname and IP for the service you want to test.

---

## 3. Purpose

- Resolve hostnames locally without relying on public DNS.
- Useful for testing, certificate checks, or interacting with services in private or local clusters.
- Can be done permanently via `/etc/hosts` or temporarily with `curl --resolve`.

---

## 4. Flushing DNS Cache

After editing `/etc/hosts`, flush the DNS cache to apply changes:

```bash
sudo dscacheutil -flushcache  
sudo killall -HUP mDNSResponder
```
