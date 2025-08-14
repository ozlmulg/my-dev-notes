# jEnv - Java Version Manager

jEnv is a command-line tool that helps you manage multiple Java versions easily.  
jEnv is a command line tool to help you forget how to set the JAVA_HOME environment variable

---

## 1. Install jEnv

```bash
brew install jenv
```

---

## 2. Configure your shell

Add jEnv to your shell environment:

### Bash

```bash
echo 'export PATH="$HOME/.jenv/bin:$PATH"' >> ~/.bash_profile
echo 'eval "$(jenv init -)"' >> ~/.bash_profile
source ~/.bash_profile # Reload the shell
```

### Zsh

```bash
echo 'export PATH="$HOME/.jenv/bin:$PATH"' >> ~/.zshrc
echo 'eval "$(jenv init -)"' >> ~/.zshrc
source ~/.zshrc # Reload the shell
```

---

## 3. Enable the export plugin

Enable plugins to automatically set `JAVA_HOME` and integrate with Maven:

```bash
eval "$(jenv init -)"
jenv enable-plugin export
jenv enable-plugin maven
```

Restart your shell:

```bash
exec $SHELL -l
```

## 4. Add JDKs/JREs

Make sure the paths match the JDKs you have installed.

```bash
jenv add /System/Library/Java/JavaVirtualMachines/1.6.0.jdk/Contents/Home
jenv add /Library/Java/JavaVirtualMachines/jdk17011.jdk/Contents/Home
jenv add /Library/Java/JavaVirtualMachines/jdk1.8.0.jdk/Contents/Home 
jenv add /Library/Java/JavaVirtualMachines/jdk-21.jdk/Contents/Home
```

---

## 5. List managed JDKs

```bash
$ jenv versions
  system
  oracle64-1.6.0.39
* oracle64-1.7.0.11 (set by /Users/hikage/.jenv/version)
```

---

## 6. Configure global version

```bash
jenv global oracle64-1.6.0.39
```

---

## 7. Configure local version (per directory)

```bash
jenv local oracle64-1.6.0.39
```

---

## 8. Configure shell instance version

```bash
jenv shell oracle64-1.6.0.39
```

---

## 9. Verify

Verify the current jenv version:

```bash
jenv version
```

Example output:

```
21.0 (set by /Users/youruser/.jenv/version)
```

Verify the current Java version:

```bash
java -version
```

Example output:

```
java version "21.0.4" 2024-07-16 LTS
Java(TM) SE Runtime Environment (build 21.0.4+8-LTS-274)
Java HotSpot(TM) 64-Bit Server VM (build 21.0.4+8-LTS-274, mixed mode, sharing)
```

Verify the java version on maven:

```bash
mvn -version
```

Example output:

```
Apache Maven 3.9.6
Java version: 21.0.4, vendor: Oracle Corporation
```

---

## Troubleshooting

**Problem:**

```
/Users/username/.jenv/shims/java: line 21: /usr/local/Cellar/jenv/0.5.6/libexec/libexec/jenv: No such file or directory
```

**Solution:**  
Refer to the [GitHub issue #394](https://github.com/jenv/jenv/issues/394):

```bash
jenv --version
jenv rehash
```

If `.jenv-shim` exists and causes errors, rename it, then run:

```bash
jenv rehash
```

---

## References

For more details, check the official website: [https://www.jenv.be](https://www.jenv.be)
