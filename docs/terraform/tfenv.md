# tfenv - Terraform Version Manager

**tfenv** is a Terraform version manager, similar to tools like `jenv` for Java or `nvm` for Node.js. It makes it easy
to install, switch, and manage multiple versions of Terraform on your system.

---

## Installation

```bash
brew install tfenv
```

---

## Usage

### List Available Terraform Versions

```bash
tfenv list-remote
```

Example output:

```
0.12.0
0.12.0-rc1
0.12.0-beta2
0.12.0-beta1
0.11.14
...
```

---

### Install a Specific Version

```bash
tfenv install 0.11.14
```

Example output:

```
[INFO] Installing Terraform v0.11.14
[INFO] Downloading release tarball from https://releases.hashicorp.com/terraform/0.11.14/terraform_0.11.14_darwin_amd64.zip
[INFO] Installation of terraform v0.11.14 successful
[INFO] Switching to v0.11.14
[INFO] Switching completed
```

---

### Use a Specific Version

```bash
tfenv use 0.12.0
```

Example output:

```
[INFO] Switching to v0.12.0
[INFO] Switching completed
```

---

## Upgrading Terraform

For instructions on upgrading Terraform to a specific version, refer to:  
[https://stackoverflow.com/questions/56283424/upgrade-terraform-to-specific-version](https://stackoverflow.com/questions/56283424/upgrade-terraform-to-specific-version)

---

## Reference

For more details, check the official repo: [https://github.com/tfutils/tfenv](https://github.com/tfutils/tfenv)
