# Terraform Cheatsheet

## Terraform Basics

Terraform is an infrastructure-as-code tool that allows you to provision and manage cloud resources declaratively. Key
components include:

- **Providers:** Plugins that manage resources (e.g., `azurerm` for Azure, `aws` for AWS)
- **Modules:** Reusable Terraform configurations
- **State:** Maintains the current state of infrastructure
- **Workspaces/Environments:** Logical separation for different deployment stages (dev, prod, etc.)

---

## Moving or Renaming Terraform Resources

When you rename a Terraform resource in code, Terraform may try to **delete the old resource** and **create a new one**
instead of just updating its name.  
To avoid unnecessary destruction and downtime, you can use the [
`terraform state mv`](https://developer.hashicorp.com/terraform/cli/commands/state/mv) command.

**Example:**

```bash
# Move a resource in the state file from the old name to the new name
terraform state mv \
module.old_module_name.aws_s3_bucket.example \
module.new_module_name.aws_s3_bucket.example
```

**When to Use:**

- Renaming a resource in `.tf` files.
- Moving a resource to another module.
- Refactoring module structures.

**Best Practices:**

1. **Plan first** — Run `terraform plan` before and after the move to ensure no unintended changes.
2. **Backup your state** — Always back up `terraform.tfstate` before making state changes.
3. **Avoid in production without testing** — Test the move in a non-production environment first.

**Reference:**

For more details, see:  
[Terraform Rename Instead of Delete – Open Up The Cloud](https://openupthecloud.com/terraform-rename-instead-delete/)

---

## Terraform Import

When managing existing infrastructure with Terraform, `terraform import` allows you to bring existing resources into
Terraform's state.

**Example: Import an Azure Service Bus Subscription Rule**

```yaml
- name: "Terraform Import"
  run: terraform import azurerm_servicebus_subscription_rule.example /subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/microsoft.servicebus/namespaces/{namespace}/topics/{topic}/subscriptions/{subscription}/rules/{rule}
  working-directory: ${{ github.workspace }}/terraform/environments/dev
```

**Sample import commands:**

```bash
# List current state resources
terraform state list

# Remove resource from state if it no longer exists
terraform state rm module.example.azurerm_dns_a_record.resource_name

# Import an existing Azure DNS A record
terraform import module.dns.azurerm_dns_a_record.resource_name /subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Network/dnszones/{zoneName}/A/{recordName}
```

**Sample import file (Terraform DNS Record State Update):**

<details>
<summary>Prerequisites</summary>

Before running the Terraform import commands, export the required Auth0 credentials in your shell profile (e.g.,
`~/.bash_profile` or `~/.zshrc`).

```bash
export AUTH0_DOMAIN="https://<your-auth0-domain>"
export AUTH0_CLIENT_ID="<your-client-id>"
export AUTH0_CLIENT_SECRET="<your-client-secret>"
```

After adding these lines, reload your shell configuration:

```bash
source ~/.bash_profile
# or
source ~/.zshrc
```

</details>

<details>
<summary>import.sh</summary>

```shell
# Terraform DNS Record State Update

## 1. View the initial state
# This lists all resources currently in the Terraform state.
terraform -chdir=.. state list

## 2. Remove existing DNS record resources from the state
terraform -chdir=.. state rm module.infrastructure_region.azurerm_dns_a_record.app_record_1
terraform -chdir=.. state rm module.infrastructure_region.azurerm_dns_a_record.app_record_2
terraform -chdir=.. state rm module.infrastructure_region.azurerm_dns_a_record.app_record_3
terraform -chdir=.. state rm module.infrastructure_region.azurerm_dns_a_record.app_record_4
terraform -chdir=.. state rm module.infrastructure_region.data.azurerm_dns_zone.zone

## 3. Import new DNS record resources into the state
terraform -chdir=.. import module.dns_region.azurerm_dns_a_record.app_record_1 /subscriptions/<subscription-id>/resourceGroups/<dns-resource-group>/providers/Microsoft.Network/dnszones/example.com/A/record-1
terraform -chdir=.. import module.dns_region.azurerm_dns_a_record.app_record_2 /subscriptions/<subscription-id>/resourceGroups/<dns-resource-group>/providers/Microsoft.Network/dnszones/example.com/A/record-2
terraform -chdir=.. import module.dns_region.azurerm_dns_a_record.app_record_3 /subscriptions/<subscription-id>/resourceGroups/<dns-resource-group>/providers/Microsoft.Network/dnszones/example.com/A/record-3
terraform -chdir=.. import module.dns_region.azurerm_dns_a_record.app_record_4 /subscriptions/<subscription-id>/resourceGroups/<dns-resource-group>/providers/Microsoft.Network/dnszones/example.com/A/record-4

## 4. View the final state
terraform -chdir=.. state list
```

</details>

**Sample import file (Terraform Auth0 Resource Import):**

<details>
<summary>import.sh</summary>

```shell
# Terraform Auth0 Resource Import

## 1. View the initial state
# This lists all resources currently in the Terraform state.
terraform -chdir=.. state list

## 2. Import Auth0 resources

### Servers
terraform -chdir=.. import module.auth0-example-service.auth0_resource_server.rs[0] <resource-server-id>

### Clients
terraform -chdir=.. import module.auth0-example-app.auth0_client.application <client-id>

### Client Grants
terraform -chdir=.. import module.auth0-example-grant.auth0_client_grant.application-grant <client-grant-id>

## 3. View the final state
terraform -chdir=.. state list
```

</details>

---

## Terraform State Management

Managing the Terraform state file is critical for avoiding conflicts and ensuring proper tracking of resources.

- To manually edit state:

```bash
# Pull state locally
terraform state pull > terraform.tfstate

# Edit terraform.tfstate file carefully

# Push edited state back
terraform state push terraform.tfstate
```

- To list resources in state:

```bash
terraform state list
```

- To remove resources from state:

```bash
terraform state rm <resource_address>
```

---

## Terraform Aliases and Providers

Terraform allows configuring multiple provider instances with aliases, useful for managing different subscriptions or
environments.

```hcl
provider "azurerm" {
  version         = "=2.33.0"
  alias           = "development"
  subscription_id = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
  features {}
}

data "azurerm_dns_zone" "example" {
  provider            = azurerm.development
  name                = "example.dev"
  resource_group_name = "dns-example-group"
}
```

---

## Terraform Console Examples

Terraform Console is a useful interactive REPL to test expressions:

```bash
$ echo 'replace("example_name", "_", "-")' | terraform console
example-name

$ echo 'split(",", "foo,bar,baz")' | terraform console
["foo", "bar", "baz"]
```

---

## Flattening Nested Lists in Terraform

When working with nested lists or complex data structures in Terraform, you can use the `flatten()` function to produce
a single-level list from multiple nested lists.

**Example: Flatten Database Roles:**

Suppose you have a list of databases, each with its own set of roles, and you want to create a flat list of all roles
with their corresponding database details.

```
variable "databases" {
  type = list(object({
    name         = string
    key_vault_id = string
    roles = list(object({
      name       = string
      privileges = list(string)
    }))
  }))
  description = "Database settings"
  default     = []
}

local all_database_roles = flatten([
  for database in local.databases : [
    for role in database.roles : {
      db_name      = database.name
      key_vault_id = database.key_vault_id
      name         = role.name
      privileges   = role.privileges
    }
  ]
])
```

**How It Works:**

1. The outer `for` loop iterates over each database in `local.databases`.
2. The inner `for` loop iterates over each role in `database.roles`.
3. For each role, we construct an object containing some fields from the database and the role itself.
4. The result of each inner loop is a list of role objects per database.
5. The `flatten()` function merges all these lists into a single list of role objects.

**Reference:**

For more details, see the [Terraform
`flatten()` function documentation](https://developer.hashicorp.com/terraform/language/functions/flatten) and [
`setproduct()` function documentation](https://developer.hashicorp.com/terraform/language/functions/setproduct).

---

## Makefile Commands for Terraform

Example Makefile snippets to standardize Terraform commands:

<details>
<summary>terraform.make</summary>

```makefile
MODULE_TERRAFORM=.

##@ TERRAFORM

terraform-init: ## Init tf, sample usage: make terraform-init env=dev
	terraform -chdir=terraform/environments/$(env) init -upgrade

terraform-format-check: ## Check formatting of tf files
	terraform fmt -check -recursive

terraform-format: ## Format tf files
	terraform fmt -recursive

terraform-validate: ## Validate tf files, sample usage: make terraform-validate env=dev
	terraform -chdir=terraform/environments/$(env) validate

terraform-state-list: ## List tf state, sample usage: make terraform-state-list env=dev
	terraform -chdir=terraform/environments/$(env) state list

terraform-plan: ## Plan tf, sample usage: make terraform-plan env=dev
	terraform -chdir=terraform/environments/$(env) plan -lock=false

terraform-import: ## Run tf import script, sample usage: make terraform-import env=dev
	(cd terraform/environments/$(env) && ./imports.sh)
```

</details>

---

## Atlantis Integration

Atlantis automates Terraform workflows on GitHub pull requests.

Sample commands for Atlantis:

```bash
atlantis plan -d 'path/to/module' -- -target='module.example'

atlantis apply -d 'path/to/module'
```

You can specify multiple targets in one command:

```bash
atlantis plan -d 'path/to/module' -- -target='module.repo1' -target='module.repo2'
```

---

## Using Specific Git References for Terraform Modules

When sourcing Terraform modules from a Git repository, omitting the `ref` parameter will default to the repository's
`master` (or `main`) branch.  
To ensure reproducibility and stability, it is recommended to use a specific tag, commit, or branch via the `ref`
parameter.

**Example:**

```hcl
# Without ref — defaults to master/main branch
source = "git::ssh://git@github.com/organization/terraform-modules.git//path/to/module"

# With ref — pinned to a specific version
source = "git::ssh://git@github.com/organization/terraform-modules.git//path/to/module?ref=v1.2.3"

# You can also reference a specific branch or commit
source = "git::ssh://git@github.com/organization/terraform-modules.git//path/to/module?ref=feature/my-feature"
source = "git::ssh://git@github.com/organization/terraform-modules.git//path/to/module?ref=commit-sha"
```

**Tip:**  
Using a specific tag or commit hash prevents unexpected changes from upstream that could break your infrastructure.

---

## Terraform Best Practices

- Use version pinning for providers to avoid unexpected changes.
- Use modules to structure reusable code.
- Separate environments using workspaces or directory structure.
- Manage state securely with remote backends.
- Use `terraform fmt` and `terraform validate` regularly.
- Plan and apply with targeted resources if needed:

```bash
terraform plan -target='module.example.resource'

terraform apply -target='module.example.resource'
```

- Use community best practices,
  e.g., [terraform-aws-lambda](https://github.com/terraform-aws-modules/terraform-aws-lambda).

---

## Sample Terraform Registries

- Refer to
  the [official Azure PostgreSQL Terraform resource documentation](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/postgresql_database)
  for managing PostgreSQL databases on Azure.

- Refer to
  the [official GitHub Repository Terraform resource documentation](https://registry.terraform.io/providers/integrations/github/latest/docs/data-sources/repository#squash_merge_commit_message)
  for managing GitHub repositories.

---

## References

For all official Terraform documentation, including CLI commands, configuration language functions, providers, modules,
and other resources, you can refer to the main HashiCorp Terraform documentation site:

- **Terraform Official Documentation
  **: [https://developer.hashicorp.com/terraform](https://developer.hashicorp.com/terraform)

This site covers:

- **[Terraform CLI](https://developer.hashicorp.com/terraform/cli)**:
  Covers all CLI commands, flags, environment variables, configuration options, and usage examples.

- **[Terraform Language & Functions](https://developer.hashicorp.com/terraform/language)**:
  Includes information about Terraform configuration language, expressions, and built-in functions like `flatten`,
  `setproduct`, `lookup`, `templatefile`, and many others.

- **[Providers & Modules](https://developer.hashicorp.com/terraform/tutorials/modules)**:
  Central resource for providers, modules, and other Terraform resources.

- **[State Management](https://developer.hashicorp.com/terraform/tutorials/state)**:
  Guides for managing Terraform state, remote backends, and best practices.

- **[Terraform Cloud & Enterprise](https://developer.hashicorp.com/terraform/tutorials/cloud-get-started):**
  Documentation for Terraform Cloud and Enterprise features like integration guides and workflows.

- **[Getting Started Guides](https://developer.hashicorp.com/terraform/tutorials):**
  Step-by-step tutorials to help beginners set up Terraform and learn best practices.

By visiting the link above, you can navigate to all specific areas of Terraform documentation in one place.
