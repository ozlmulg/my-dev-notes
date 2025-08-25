# Internet Speed & IP

## 1. Test Your Internet Speed

You can quickly check your internet speed using online tools or command-line options.

### Online Tools

- [Fast.com](https://fast.com) – Simple, shows download speed immediately.
- [Speedtest.net](https://www.speedtest.net) – Provides download, upload, and ping statistics.
- [SpeedOf.Me](https://speedof.me) – HTML5-based, works on all devices.

> Click **Start** or **Go** to measure your download and upload speeds.
> Wait for the test to finish. Most tools also show latency (ping) and sometimes jitter.

### Terminal Tools

- **Linux/macOS:** Install speedtest-cli

For Debian/Ubuntu:

```bash
sudo apt install speedtest-cli
```

For macOS with Homebrew:

```bash
brew install speedtest-cli
```

Then run:

```bash
speedtest
```

- **Windows:** Install via Python pip

```shell
pip install speedtest-cli
```

Then run:

```shell
speedtest
```

> These commands provide download/upload speed and ping directly in your terminal.

---

## 2. Find Your Local IP Address

Your local IP is assigned to your active network interface (usually `en0`, `wlan0`, or `eth0`).

### macOS / Linux (Terminal)

1. Open Terminal.
2. Run the command: `ifconfig` or `ip addr` (on modern Linux)
3. Find the IP assigned to your active network interface (usually `en0`, `wlan0`, or `eth0`).

Example output (with example IPs):

```text
en0: flags=8863<UP,BROADCAST,SMART,RUNNING> mtu 1500
inet 192.168.1.42 netmask 0xffffff00 broadcast 192.168.1.255
```

- `en0` is the interface name.
- inet `192.168.1.42` is your local IP address.

### Windows (Command Prompt)

1. Open Command Prompt.
2. Run: `ipconfig`.
3. Look for **IPv4 Address** under your active network adapter.
4. Example output:

Wireless LAN adapter Wi-Fi:

```text
IPv4 Address. . . . . . . . . . . : 192.168.1.42
Subnet Mask . . . . . . . . . . . : 255.255.255.0
Default Gateway . . . . . . . . . : 192.168.1.1
```

- `192.168.1.42` is your local IP address.

### Router Interface

- Log in to your router’s admin page (usually `http://192.168.0.1` or `http://192.168.1.1`).
- Look for **Device List** or **Connected Devices**.
- Your device IP will be shown next to its name/MAC address.

---

## 3. Find Your Public IP Address

Your public IP is visible to the internet and can be found using online tools:

- [https://whatismyipaddress.com](https://whatismyipaddress.com)
- [https://ipchicken.com](https://ipchicken.com)
- [https://ifconfig.me](https://ifconfig.me) in a browser or terminal: `curl ifconfig.me`
- Open the website; your public IP will be displayed.
- Example output: `203.0.113.17` (example IP)

---

## 4. How Local and Public IPs Work Together?

![](<images/local_public_ip.png>)

### Local IP Address (Private IP)

**Definition:** This is the IP assigned to your device by your local network (home router, office network, etc.).

**Typical format:** IPv4 addresses like `192.168.1.42`, `10.0.0.5`, or `172.16.0.10`.

**Purpose:**

- It allows devices on the **same local network** to communicate.
- Examples: your laptop talks to your printer, smart TV, or other computers on the same Wi-Fi.

**How it works:**

- Your router runs a DHCP service, which assigns each device a unique local IP automatically when it joins the network.
- This IP is **not visible on the internet**, only inside your network.

### Public IP Address

**Definition:** This is the IP assigned to your network by your Internet Service Provider (ISP).

**Example:** `203.0.113.17` (used only as an example IP)

**Purpose:**

- Allows your **network to communicate with the internet**.
- All devices inside your home share this public IP when accessing websites or online services.

**How it works:**

- Your router uses **Network Address Translation (NAT)** to map your local IPs to the public IP.
- When a request goes to a website, the router replaces the local IP in the outgoing packet with the public IP.
- When the website replies, the router forwards the response to the correct device inside your network.

---

## Tips

- For consistent speed results, close other apps using the internet before testing.
- Public IPs can change depending on your ISP (dynamic IP). Local IPs are usually stable unless manually configured.
- For automation or frequent testing, `speedtest-cli` is very convenient in scripts.

---

## Summary

- Use online tools or `speedtest-cli` to check download/upload speeds.
- Find your local IP via `ifconfig`, `ip addr`, or `ipconfig`.
- Check your public IP using websites or curl.
