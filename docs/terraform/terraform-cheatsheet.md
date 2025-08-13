# Terraform Cheatsheet

## Terraform Basics

Terraform is an infrastructure-as-code tool that allows you to provision and manage cloud resources declaratively. Key
components include:

- **Providers:** Plugins that manage resources (e.g., `azurerm` for Azure, `aws` for AWS)
- **Modules:** Reusable Terraform configurations
- **State:** Maintains the current state of infrastructure
- **Workspaces/Environments:** Logical separation for different deployment stages (dev, prod, etc.)

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

## Terraform Console Examples

Terraform Console is a useful interactive REPL to test expressions:

```bash
$ echo 'replace("example_name", "_", "-")' | terraform console
example-name

$ echo 'split(",", "foo,bar,baz")' | terraform console
["foo", "bar", "baz"]
```

---

## Makefile Commands for Terraform

Example Makefile snippets to standardize Terraform commands:

<details>
<summary>terraform.sh</summary>

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

## Sample Terraform Registries

- Refer to
  the [official Azure PostgreSQL Terraform resource documentation](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/postgresql_database)
  for managing PostgreSQL databases on Azure.

- Refer to
  the [official GitHub Repository Terraform resource documentation](https://registry.terraform.io/providers/integrations/github/latest/docs/data-sources/repository#squash_merge_commit_message)
  for managing GitHub repositories.

---

## Additional Notes

- To use specific git references for modules:

```hcl
source = "git::ssh://git@github.com/organization/terraform-modules.git//path/to/module?ref=v1.2.3"
```

- Flatten nested lists in variables using `flatten` and `for` expressions to manage complex inputs effectively.
