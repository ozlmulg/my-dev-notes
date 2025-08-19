# Bash Completion

## Introduction

**Bash completion** is a shell feature that allows you to auto-complete commands, options, and arguments by pressing
the <kbd>Tab</kbd> key.  
This is especially useful for CLI tools like `git`, `docker`, `kubectl`, and `multipass` where you often need to
remember many commands and flags.

In this guide, we'll:

- Install `bash-completion` using Homebrew.
- Enable bash completion in your shell.
- Set up completion for **Git**, **Docker**, **kubectl**, and **Multipass**.

---

## Installing Bash Completion

Bash Completion comes in two major versions, and the right one for you depends on your Bash version.

| Feature                      | bash-completion v1                   | bash-completion v2                               |
|------------------------------|--------------------------------------|--------------------------------------------------|
| Compatible Bash versions     | Bash 3.x and above                   | Bash 4.x and above (including 5.x)               |
| Status                       | Legacy, still works for older setups | Recommended for modern setups                    |
| Installation path (Homebrew) | `/usr/local/etc/bash_completion.d/`  | `$(brew --prefix)/etc/profile.d/`                |
| Feature set                  | More limited                         | Expanded features and modern completion format   |
| macOS usage                  | Works with macOS default Bash (3.2)  | Best used with newer Bash installed via Homebrew |

**When to choose v1:**

- You are on macOS and using the default Bash (3.2) without upgrading.
- You need maximum compatibility with older scripts or legacy environments.

**When to choose v2:**

- You have installed a newer Bash (4.x or 5.x) via Homebrew.
- You want more up-to-date and feature-rich completions.

**Installation commands:**

- **v1:**

```bash
brew install bash-completion
```

- **v2:**

```bash
brew install bash-completion@2
```

---

## Enabling Bash Completion

To enable bash completion globally, add the following to your `~/.bash_profile` (or `~/.bashrc` if you prefer):

- **If you installed ` bash-completion`:**

```bash
if [ -f $(brew --prefix)/etc/bash_completion ]; then
  . $(brew --prefix)/etc/bash_completion
fi
```

- **If you installed ` bash-completion@2`:**

```bash
if [ -f $(brew --prefix)/etc/profile.d/bash_completion.sh ]; then
    . $(brew --prefix)/etc/profile.d/bash_completion.sh
fi
```

Then reload your profile:

```bash
source ~/.bash_profile
```

---

## Git Bash Completion

Git also supports bash tab completion to make typing commands and branch names faster.

Add alias `g` for `git` in your `~/.aliases` file:

```bash
alias g="git"
```

Source the git-completion script if it exists:

```bash
if [ -f ~/.git-completion.bash ]; then
    source ~/.git-completion.bash
fi
```

Set up tab completion for the 'g' alias if bash completion is available:

```bash
if type _git &> /dev/null && [ -f /usr/local/etc/bash_completion.d/git-completion.bash ]; then
  complete -o default -o nospace -F _git g;
fi;
```

Reload your shell:

```bash
source ~/.aliases
source ~/.bash_profile
```

You can now type:

```bash
git <TAB>
g <TAB>
``` 

and see available commands.

---

## Docker Bash Completion

Add alias `d` for `docker` in your `~/.aliases` file:

```bash
alias d="docker"
```

Create a dedicated directory for Docker completions and download the script:

```bash
mkdir -p ~/.docker/completion
curl -L https://raw.githubusercontent.com/docker/cli/master/contrib/completion/bash/docker \
    -o ~/.docker/completion/docker
```

Source the completion script in your `~/.bash_profile`:

```bash
echo 'source ~/.docker/completion/docker' >> ~/.bash_profile
echo 'complete -F _docker d' >> ~/.bash_profile # optional alias completion
```

or manually add these lines to your `~/.bash_profile`:

```bash
source ~/.docker/completion/docker
complete -F _docker d # optional alias completion
```

Reload your shell:

```bash
source ~/.aliases
source ~/.bash_profile
```

You can now type:

```bash
docker <TAB>
d <TAB>
``` 

and see available commands.

---

## kubectl Bash Completion

Add alias `k` for `kubectl` in your `~/.aliases` file:

```bash
alias k="kubectl"
```

Source the completion script in your `~/.bash_profile`:

```bash
echo 'source /usr/local/opt/kubernetes-cli/etc/bash_completion.d/kubectl' >> ~/.bash_profile
#echo 'source <(kubectl completion bash)' >> ~/.bash_profile # optional for dynamic completion
echo 'complete -F __start_kubectl k' >> ~/.bash_profile # optional alias completion
```

or manually add these lines to your `~/.bash_profile`:

```bash
source /usr/local/opt/kubernetes-cli/etc/bash_completion.d/kubectl
#source <(kubectl completion bash) # optional for dynamic completion
complete -F __start_kubectl k # optional alias completion
```

Reload your shell:

```bash
source ~/.aliases
source ~/.bash_profile
```

You can now type:

```bash
kubectl <TAB>
k <TAB>
``` 

and see available commands.

---

## Multipass Bash Completion

Add alias `mp` for `multipass` in your `~/.aliases` file:

```bash
alias mp="multipass"
```

Source the completion script in your `~/.bash_profile`:

```bash
echo 'source "/Library/Application Support/com.canonical.multipass/Resources/completions/bash/multipass"' >> ~/.bash_profile
echo 'complete -F _multipass_complete mp' >> ~/.bash_profile # optional alias completion
```

or manually add these lines to your `~/.bash_profile`:

```bash
if [ -f "/Library/Application Support/com.canonical.multipass/Resources/completions/bash/multipass" ]; then
    source "/Library/Application Support/com.canonical.multipass/Resources/completions/bash/multipass"
    complete -F _multipass_complete mp
fi
```

Reload your shell:

```bash
source ~/.aliases
source ~/.bash_profile
```

You can now type:

```bash
multipass <TAB>
mp <TAB>
``` 

and see available commands.

---

## Conclusion

Bash completion can dramatically speed up your workflow when working with CLI tools.  
With these configurations, you now have tab-completion support for:

- Git
- Docker
- kubectl
- Multipass

No more typing long command names or guessing available flags—just hit <kbd>Tab</kbd> and let bash do the work.
