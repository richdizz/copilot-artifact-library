---
applyTo: '**/*.tf'
---

# Terraform Coding Instructions

## File Organization

### Project Structure
```
terraform/
├── environments/
│   ├── dev/
│   │   ├── main.tf
│   │   ├── variables.tf
│   │   └── terraform.tfvars
│   └── prod/
├── modules/
│   ├── networking/
│   ├── compute/
│   └── storage/
└── shared/
    └── provider.tf
```

### File Types
- `main.tf`: Primary configuration file
- `variables.tf`: Variable declarations
- `outputs.tf`: Output definitions
- `versions.tf`: Provider and version constraints
- `terraform.tfvars`: Variable values
- `backend.tf`: State configuration

## Code Organization

### Module Structure
```hcl
# Provider configuration
terraform {
  required_providers {
    azurerm = {
      source  = "hashicorp/azurerm"
      version = "~> 3.0"
    }
  }
}

# Variables
variable "environment" {
  type        = string
  description = "Environment name"
}

# Resources
resource "azurerm_resource_group" "example" {
  name     = "example-rg"
  location = "westus2"
}

# Outputs
output "resource_group_id" {
  value = azurerm_resource_group.example.id
}
```

## Naming Conventions

### Resource Names
- Use snake_case for resource names
- Include purpose and environment
- Follow cloud provider restrictions
- Use consistent naming patterns
- Include resource type prefix

```hcl
resource "azurerm_storage_account" "example" {
  name                = "${var.prefix}stor${random_string.suffix.result}"
  resource_group_name = azurerm_resource_group.example.name
}
```

### Variable Names
- Use snake_case
- Be descriptive
- Group related variables
- Include type information if not obvious
- Use consistent prefixes

```hcl
variable "storage_account_tier" {
  type        = string
  description = "The storage account tier (Standard or Premium)"
  default     = "Standard"
}
```

## Code Style

### Indentation and Spacing
- Use 2 spaces for indentation
- Add blank lines between blocks
- Align equal signs and braces
- Use consistent spacing
- Group related resources

### Comments and Documentation
- Document all variables
- Add block comments for complex logic
- Include usage examples
- Document dependencies
- Add reference links

```hcl
# Storage account for application data
# Configured with:
# - Blob storage enabled
# - Static website hosting
# - HTTPS only
resource "azurerm_storage_account" "app_storage" {
  # ...
}
```

## Best Practices

### State Management
- Use remote state
- Enable state locking
- Implement workspace isolation
- Back up state files
- Use state data sources

```hcl
terraform {
  backend "azurerm" {
    resource_group_name  = "terraform-state-rg"
    storage_account_name = "tfstate"
    container_name      = "tfstate"
    key                 = "prod.terraform.tfstate"
  }
}
```

### Module Design
- Create reusable modules
- Use clear interfaces
- Include documentation
- Version modules
- Test thoroughly

```hcl
module "storage" {
  source = "../modules/storage"
  
  resource_group_name = var.resource_group_name
  location           = var.location
  environment        = var.environment
}
```

### Resource Configuration
- Use data sources appropriately
- Implement proper dependencies
- Enable monitoring
- Configure backups
- Use managed identities

### Security
- Use variables for sensitive data
- Implement least privilege
- Enable encryption
- Configure network security
- Use key vault for secrets

## Testing and Validation

### Pre-deployment Testing
- Run terraform fmt
- Run terraform validate
- Check for security issues
- Test with different vars
- Use terraform plan

### Deployment Validation
- Monitor apply process
- Verify resource creation
- Test connectivity
- Validate configurations
- Check outputs

## Resource Specific Guidelines

### Storage Account
```hcl
resource "azurerm_storage_account" "example" {
  name                     = var.storage_account_name
  resource_group_name      = azurerm_resource_group.example.name
  location                 = azurerm_resource_group.example.location
  account_tier             = "Standard"
  account_replication_type = "LRS"
  
  network_rules {
    default_action = "Deny"
    ip_rules       = var.allowed_ips
  }
  
  tags = var.tags
}
```

### Virtual Network
```hcl
resource "azurerm_virtual_network" "example" {
  name                = var.vnet_name
  resource_group_name = azurerm_resource_group.example.name
  location            = azurerm_resource_group.example.location
  address_space       = var.address_space
  
  subnet {
    name           = "subnet1"
    address_prefix = var.subnet_prefix
  }
  
  tags = var.tags
}
```

## Variables and Outputs

### Variable Declaration
```hcl
variable "environment" {
  type        = string
  description = "Environment name (dev, staging, prod)"
  validation {
    condition     = contains(["dev", "staging", "prod"], var.environment)
    error_message = "Environment must be dev, staging, or prod."
  }
}
```

### Output Definition
```hcl
output "storage_account_id" {
  description = "The ID of the storage account"
  value       = azurerm_storage_account.example.id
  sensitive   = false
}
```

## Error Handling

### Resource Dependencies
```hcl
resource "azurerm_app_service" "example" {
  name                = var.app_service_name
  location            = azurerm_resource_group.example.location
  resource_group_name = azurerm_resource_group.example.name
  app_service_plan_id = azurerm_app_service_plan.example.id
  
  depends_on = [
    azurerm_app_service_plan.example
  ]
}
```

### Condition Handling
```hcl
resource "azurerm_key_vault" "example" {
  count               = var.create_key_vault ? 1 : 0
  name                = var.key_vault_name
  resource_group_name = azurerm_resource_group.example.name
  location            = azurerm_resource_group.example.location
}
```

## Deployment Guidelines

### Environment Management
- Use workspaces or separate states
- Implement progressive deployment
- Configure environment-specific variables
- Use proper backend configuration
- Document deployment process

### Rollout Strategy
- Use terraform plan
- Implement proper dependencies
- Consider regional deployment
- Plan for rollbacks
- Document steps and processes
