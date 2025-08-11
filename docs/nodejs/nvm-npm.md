# nvm & npm Setup and Usage Guide

## Introduction

**nvm** (Node Version Manager) allows you to install and switch between multiple Node.js versions easily.  
**npm** (Node Package Manager) is used to manage JavaScript packages for Node.js.

---

## 1. Installation

```bash
brew install nvm
```

---

## 2. Configure NVM

Create a directory for NVM:

```bash
mkdir ~/.nvm
```

Update your shell configuration (`~/.bash_profile` or `~/.zshrc`):

```bash
# NVM
export NVM_DIR="$HOME/.nvm"
  [ -s "$(brew --prefix)/opt/nvm/nvm.sh" ] && . "$(brew --prefix)/opt/nvm/nvm.sh" # This loads nvm
  [ -s "$(brew --prefix)/opt/nvm/etc/bash_completion.d/nvm" ] && . "$(brew --prefix)/opt/nvm/etc/bash_completion.d/nvm" # This loads nvm
```

Reload your shell:

```bash
source ~/.bash_profile
```

---

## 3. Basic Commands

### Check nvm version

```bash
nvm --version
```

### Install a specific Node.js version

```bash
nvm install 16.20.2
```

### List installed Node.js versions

```bash
nvm list
```

### List available remote versions

```bash
nvm list-remote
```

### Use a specific version

```bash
nvm use 16.20.2
```

### Set a default version

```bash
nvm alias default 16.20.2
```

---

## 4. Node & NPM Versions

Check versions:

```bash
node -v # node --version
npm -v # npm --version
```

---

## 5. Using `.nvmrc` and `.npmrc`

### `.nvmrc`

If your project contains a `.nvmrc` file:

```text
v16.20.2
```

You can run:

```bash
nvm use
```

to automatically switch to that version.

### `.npmrc`

Example `.npmrc` for private GitHub packages:

```text
@yourorg:registry=https://npm.pkg.github.com
//npm.pkg.github.com/:_authToken=ghp_your_github_token_here
```

---

## 6. Example Makefile Commands

### Generate `.npmrc`

```make
init-npmrc: ## Generate npmrc file to access @yourorg npm packages
	echo -e "@yourorg:registry=https://npm.pkg.github.com\n//npm.pkg.github.com/:_authToken=${GITHUB_TOKEN}" >${MAIN_POM_FOLDER}/app/test-react-client/.npmrc
```

### Install React Client

```make
react-install: ## Install react client
	rm -rf ${MAIN_POM_FOLDER}/app/test-react-client/node \
	       ${MAIN_POM_FOLDER}/app/test-react-client/node_modules
	make init-npmrc
	cd ${MAIN_POM_FOLDER}/app/test-react-client && \
	bash -i -c "nvm use && npm install"
```

### Run React Client

```make
react-run: ## Run react client
	make init-npmrc
	cd ${MAIN_POM_FOLDER}/app/test-react-client && \
	bash -i -c "nvm use && npm start"
```

---

## 7. Useful Commands

### Kill a process on port 3000

```bash
lsof -i tcp:3000
kill -9 <PID>
```

### Webpack Installation

```bash
npm install --save-dev webpack@4
webpack --version
npm run dev
```

---

## 8. Directory Structure

```text
~/.nvm
   └─ versions
      └─ node
         ├─ v11.14.0
         │  └─ lib
         │     └─ node_modules
         │        └─ npm
         └─ v12.4.0
            └─ lib
               └─ node_modules
                  └─ npm
```

---

## References

- [NVM GitHub](https://github.com/nvm-sh/nvm)
- [Node.js Website](https://nodejs.org/)
- [NPM Documentation](https://docs.npmjs.com/)
- [Homebrew NVM Formula](https://formulae.brew.sh/formula/nvm)
- [Webpack](https://webpack.js.org/guides/installation/)
