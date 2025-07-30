---
applyTo: '**/*.tf'
---

# Terraform Code Standards

## 1. File Structure Standards

### Project Organization
```
project/
├── README.md
├── main.tf
├── variables.tf
├── outputs.tf
├── versions.tf
├── locals.tf
└── terraform.tfvars
```

### Module Organization
```
modules/
└── module_name/
    ├── README.md
    ├── main.tf
    ├── variables.tf
    ├── outputs.tf
    └── examples/
        └── basic/
            ├── main.tf
            └── terraform.tfvars
```

## 2. Naming Standards

### Resource Names
- Use snake_case
- Be descriptive
- Include purpose
- Follow provider conventions
- Use consistent patterns

### Variable Names
```hcl
# Good
variable "environment_name" {
  type        = string
  description = "The name of the environment"
}

# Bad
variable "env" {
  type = string
}
```

### Output Names
```hcl
# Good
output "storage_account_primary_connection_string" {
  value     = azurerm_storage_account.example.primary_connection_string
  sensitive = true
}

# Bad
output "storage_conn" {
  value = azurerm_storage_account.example.primary_connection_string
}
```

## 3. Code Format Standards

### Indentation
```hcl
# Good
resource "azurerm_resource_group" "example" {
  name     = var.resource_group_name
  location = var.location
  
  tags = {
    Environment = var.environment
    Owner       = var.owner
  }
}

# Bad
resource "azurerm_resource_group" "example" {
name = var.resource_group_name
location = var.location
tags = {
Environment = var.environment
Owner = var.owner
}
}
```

### Alignment
```hcl
# Good
module "network" {
  source              = "../modules/network"
  resource_group_name = azurerm_resource_group.example.name
  location           = var.location
  address_space      = var.address_space
}

# Bad
module "network" {
  source = "../modules/network"
  resource_group_name=azurerm_resource_group.example.name
  location=var.location
  address_space=var.address_space
}
```

## 4. Documentation Standards

### File Headers
```hcl
# Description: This file contains the main infrastructure configuration
# Author: Name
# Date: YYYY-MM-DD
# Version: 1.0.0
```

### Resource Documentation
```hcl
# Storage account for application data
# - Configured with blob storage
# - HTTPS only
# - Network rules applied
resource "azurerm_storage_account" "example" {
  # ...
}
```

### Variable Documentation
```hcl
variable "storage_account_name" {
  type        = string
  description = <<EOT
Name of the storage account.
Must be:
- Between 3 and 24 characters
- Lowercase letters and numbers only
- Globally unique
EOT
}
```

## 5. Security Standards

### Authentication
```hcl
provider "azurerm" {
  features {}
  
  # Use managed identity or service principal
  use_msi = true
}
```

### Sensitive Data
```hcl
variable "admin_password" {
  type        = string
  sensitive   = true
  description = "The administrator password for the VM"
}
```

### Network Security
```hcl
resource "azurerm_storage_account" "example" {
  # ...
  
  network_rules {
    default_action = "Deny"
    ip_rules       = var.allowed_ips
    bypass         = ["AzureServices"]
  }
}
```

## 6. Resource Standards

### Resource Groups
```hcl
resource "azurerm_resource_group" "example" {
  name     = "${var.prefix}-${var.environment}-rg"
  location = var.location
  
  tags = merge(var.common_tags, {
    environment = var.environment
    managed_by  = "terraform"
  })
}
```

### Virtual Networks
```hcl
resource "azurerm_virtual_network" "example" {
  name                = "${var.prefix}-vnet"
  resource_group_name = azurerm_resource_group.example.name
  location            = azurerm_resource_group.example.location
  address_space       = var.address_space
  
  tags = var.tags
}
```

## 7. Module Standards

### Interface Design
```hcl
# Clear inputs
variable "resource_group_name" {
  type        = string
  description = "Name of the resource group"
}

# Meaningful outputs
output "virtual_network_id" {
  value       = azurerm_virtual_network.example.id
  description = "The ID of the virtual network"
}
```

### Module Usage
```hcl
module "storage" {
  source = "./modules/storage"
  
  # Required parameters first
  resource_group_name = azurerm_resource_group.example.name
  location           = var.location
  
  # Optional parameters after
  tier               = "Standard"
  replication_type   = "LRS"
}
```

## 8. State Management Standards

### Backend Configuration
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

### State Isolation
- Use separate state files per environment
- Use workspaces for feature development
- Implement state locking
- Regular state backups
- Document state layout

## 9. Testing Standards

### Module Testing
- Include examples
- Document test cases
- Validate inputs
- Test configurations
- Check outputs

### Infrastructure Testing
- Use terraform validate
- Run security scans
- Test with terratest
- Validate compliance
- Check costs

## 10. Version Control Standards

### Branch Strategy
- Use feature branches
- Implement PR reviews
- Tag releases
- Document changes
- Use semantic versioning

### Repository Structure
- Clear README
- License file
- .gitignore
- Examples folder
- Documentation folder
