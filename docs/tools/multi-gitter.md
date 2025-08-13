# Multi-Gitter

This document introduces a powerful tool called **Multi-Gitter**, perfect for people who need to apply changes across
many repositories efficiently.

Multi-Gitter runs a script on multiple repositories, automatically creating Pull Requests (PRs) with custom titles and
bodies. This allows teams to review and merge changes consistently across all relevant repos.

---

## Why Use Multi-Gitter?

- Automate repetitive changes across multiple repositories.
- Keep repo configurations consistent.
- Easily update or remove files (e.g., config files, CODEOWNERS).
- Facilitate team collaboration with reviewable PRs.

---

## Example Use Cases

- Removing deprecated config files from workflow directories in multiple repos.
- Updating CODEOWNERS files to reflect new team ownership.
- Bulk modifications related to platform migrations or tooling upgrades.

---

## Installation

On macOS, install via Homebrew:

```bash
brew install lindell/multi-gitter/multi-gitter
```

If you encounter errors related to Command Line Tools, run:

```bash
sudo rm -rf /Library/Developer/CommandLineTools
sudo xcode-select --install
```

---

## Prepare Your Scripts

Make sure your shell scripts have execute permission:

```bash
chmod +x ./update_codeowners.sh
```

---

## Usage Examples

### 1. Delete Config Files in Multiple Repos

Dry-run to preview:

```bash
multi-gitter run ./delete_config.sh \
--token YOUR_GITHUB_TOKEN \
--repo user/repo1,user/repo2,user/repo3 \
--branch feature/remove-config \
--pr-title "Remove config.yml from workflows" \
--pr-body "This PR removes the unnecessary config.yml file from workflow directories." \
--dry-run
```

Execute actual PR creation:

```bash
multi-gitter run ./delete_config.sh \
--token YOUR_GITHUB_TOKEN \
--repo user/repo1,user/repo2,user/repo3 \
--branch feature/remove-config \
--pr-title "Remove config.yml from workflows" \
--pr-body "This PR removes the unnecessary config.yml file from workflow directories."
```

### 2. Update CODEOWNERS Across Multiple Repos

Dry-run example:

```bash
multi-gitter run ./update_codeowners.sh \
--token YOUR_GITHUB_TOKEN \
--repo user/repo1,user/repo2,user/repo3 \
--branch feature/update-codeowners \
--pr-title "Update CODEOWNERS" \
--pr-body "This PR updates the CODEOWNERS file with correct team assignments." \
--dry-run
```

---

## Sample Scripts

***Delete config.yml:***

<details>
<summary>delete_config.sh</summary>

```bash
#!/bin/bash

# Check if config.yml exists and remove it
if [ -f ".github/config.yml" ]; then
  echo "Found config.yml. Removing..."
  git rm .github/config.yml
else
  echo "config.yml not found, skipping..."
fi
```

</details>

***Update codeowners:***

<details>
<summary>update_codeowners.sh</summary>

```bash
#!/bin/bash

repo_name=$REPOSITORY

ORG_NAME="your-org"
CODEOWNERS_FILE=".github/CODEOWNERS"

# Example: Clear CODEOWNERS for a specific repo
if [[ "$repo_name" == "your-org/special-repo" ]]; then
  echo "Clearing CODEOWNERS for $repo_name..."
  > $CODEOWNERS_FILE
  git add $CODEOWNERS_FILE
  exit 0
fi

# Assign teams based on repo name
if [[ "$repo_name" == "your-org/repo1" ]]; then
  team="@${ORG_NAME}/team1"
elif [[ "$repo_name" == "your-org/repo2" ]]; then
  team="@${ORG_NAME}/team2"
elif [[ "$repo_name" == "your-org/repo3" ]]; then
  team="@${ORG_NAME}/team3"
else
  echo "No team assignment found for $repo_name, skipping..."
  exit 0
fi

echo "Updating CODEOWNERS for $repo_name with team $team..."

echo "* $team" > $CODEOWNERS_FILE

git add $CODEOWNERS_FILE
```

</details>



