# SDKMAN! - Java Version Manager

SDKMAN! is a tool for managing parallel versions of multiple Software Development Kits on most Unix-based systems. It
provides a convenient way to install, switch, and manage SDKs such as Java, Groovy, Kotlin, Scala, and more.

---

## Installing SDKMAN!

To install SDKMAN!, open your terminal and run:

```bash
brew install sdkman-cli
```

---

## Configuring Your Shell

To ensure SDKMAN! works correctly, you need to add initialization to your shell profile. For Bash or Zsh, append the
following lines at the end of your profile file (`~/.bash_profile`, `~/.bashrc`, or `~/.zshrc`):

```bash
#THIS MUST BE AT THE END OF THE FILE FOR SDKMAN TO WORK!!!
export SDKMAN_DIR="$HOME/.sdkman"
[[ -s "$HOME/.sdkman/bin/sdkman-init.sh" ]] && source "$HOME/.sdkman/bin/sdkman-init.sh"
```

After adding these lines, apply the changes with:

```bash
source ~/.bash_profile (or 'source ~/.bashrc' or 'source ~/.zshrc' depending on your shell)
```

You can verify that SDKMAN! is installed correctly by executing:

```bash
sdk version
```

---

## Listing Available SDKs

To see all SDKs available for installation, use:

```bash
sdk list
```

---

This will display a list of candidates, versions, and whether they are installed on your system.

## Listing Available Versions for a Specific SDK

To see all available versions of a specific SDK, such as Java, run:

```bash
sdk list java
```

This will display all Java versions you can install, along with the currently installed and default versions.

---

## Installing a Specific SDK

To install a specific version of an SDK, use:

```bash
sdk install <candidate> <version>
```

Example:

```bash
sdk install java 21.0.4-tem
```

---

## Installing Latest SDK

To install the latest version of an SDK, use:

```bash
sdk install <candidate>
```

Example:

```bash
sdk install java
```

---

## Switching Between Versions

You can switch to a different installed version with:

```bash
sdk use <candidate> <version>
```

Example:

```bash
sdk use java 21.0.4-tem
```

Or set it as the default version:

```bash
sdk default <candidate> <version>
```

Example:

```bash
sdk default java 21.0.4-tem
```

---

## Updating SDKMAN!

Keep your SDKMAN! installation up-to-date by running:

```bash
sdk update
```

---

## Uninstalling SDKs

To remove an installed SDK, use:

```bash
sdk uninstall <candidate> <version>
```

Example:

```bash
sdk uninstall java 21.0.4-tem
```

---

## Conclusion

SDKMAN! simplifies the management of multiple SDKs on your system, allowing you to install, switch, and maintain
different versions effortlessly. By mastering these commands and correctly configuring your shell, you can ensure that
your development environment is flexible and up-to-date.

For more detailed usage instructions, please refer to the official SDKMAN! usage
guide: [https://sdkman.io/usage](https://sdkman.io/usage)
