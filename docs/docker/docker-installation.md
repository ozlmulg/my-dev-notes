# Docker Installation

This guide helps you install Docker on **Mac**, **Linux**, and **Windows**, and also provides a link to try Docker
online via **Play with Docker**.

---

## Docker on Mac

Docker Desktop is the easiest way to install Docker on macOS. It includes both a GUI and CLI tools.

**Installation Steps:**

1. Download Docker Desktop from the official
   site: [Docker Desktop for Mac](https://docs.docker.com/desktop/setup/install/mac-install/)
2. Open the downloaded `.dmg` file and drag **Docker** to the **Applications** folder.
3. Launch Docker from Applications.
4. Follow the onboarding instructions to finish the setup.
5. Verify installation by opening Terminal and running:

```bash
docker --version
docker compose version
docker run hello-world
```

**Optional: Install via Homebrew CLI (Not Recommended):**

If you prefer a CLI-only installation:

```bash
brew install docker docker-compose
```

---

## Docker on Linux

For Linux, it is recommended to follow the official installation instructions for your distribution to ensure you get
the latest version.

### Ubuntu / Debian

Visit the official Docker
guide: [Docker Desktop for Ubuntu](https://docs.docker.com/desktop/setup/install/linux/ubuntu/)

### Fedora / CentOS

Visit the official Docker
guide: [Docker Desktop for Fedora](https://docs.docker.com/desktop/setup/install/linux/fedora/)

### After Installation

After installation, you may want to run Docker without `sudo`:

```bash
sudo usermod -aG docker $USER
newgrp docker
```

Verify installation by opening Terminal and running:

```bash
docker --version
docker compose version
docker run hello-world
```

---

## Docker on Windows

Docker Desktop is available for Windows 10/11 and includes both GUI and CLI tools.

1. Download Docker Desktop
   from: [Docker Desktop for Windows](https://docs.docker.com/desktop/setup/install/windows-install/)
2. Run the installer and follow the setup instructions.
3. Verify installation in PowerShell or CMD:

```bash
docker --version
docker compose version
docker run hello-world
```

---

## Play with Docker

If you want to try Docker without installing it locally:

- Go to [https://labs.play-with-docker.com/](https://labs.play-with-docker.com/)
- Create a free account or login with Docker Hub.
- Launch a playground and start experimenting with Docker commands instantly.

---

## References

- [Official Docker Documentation](https://docs.docker.com)
- [Docker announcement on Wikipedia](https://en.wikipedia.org/wiki/Docker_(software)#cite_note-docker-announcement-13)
- [The Future of Linux Containers - YouTube Video](https://www.youtube.com/watch?v=wW9CAH9nSLs)
- [Linux Kernel Overview](https://en.wikipedia.org/wiki/Kernel_(operating_system))
- [What is an Operating System? - Techopedia](https://www.techopedia.com/definition/3515/operating-system-os)
- [Everything You Need to Know About Linux Containers - Linux Journal](https://www.linuxjournal.com/content/everything-you-need-know-about-linux-containers-part-i-linux-control-groups-and-process)
- [Cgroups - Wikipedia](https://en.wikipedia.org/wiki/Cgroups)
- [Linux Namespaces - Wikipedia](https://en.wikipedia.org/wiki/Linux_namespaces)
- [Docker Engine Overview](https://docs.docker.com/engine/docker-overview/)
- [Linux Kernel - Wikipedia](https://en.wikipedia.org/wiki/Linux_kernel)
- [LXC Containers - Wikipedia](https://en.wikipedia.org/wiki/LXC)
- [Docker Container Runtime](https://www.docker.com/products/container-runtime)
- [History of Docker - TechTarget](https://searchservervirtualization.techtarget.com/feature/The-history-of-Dockers-climb-in-the-container-management-market)
- [Docker Images - TechTarget](https://searchitoperations.techtarget.com/definition/Docker-image)
- [Building Minimal Docker Containers for Go Applications](https://blog.codeship.com/building-minimal-docker-containers-for-go-applications/)
