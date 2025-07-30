---
applyTo: '**/*.bicep'
---

# Bicep Coding Instructions

## File Organization

### File Structure
- Use clear, descriptive filenames that indicate resource type and purpose
- Organize files by resource type or deployment unit
- Use separate parameter files for different environments
- Keep files focused and modular
- Use nested templates for reusable components

### File Naming
- Use kebab-case for file names: `web-app.bicep`, `key-vault.bicep`
- Parameter files should match main file name: `main.bicep` → `main.parameters.json`
- Use descriptive module names: `storage-account.bicep`, `app-service-plan.bicep`

## Code Organization

### Module Structure
```bicep
targetScope = 'resourceGroup'    // Declare target scope

// Parameters
@description('Environment name')
param environmentName string

// Variables
var location = resourceGroup().location

// Resources
resource storageAccount 'Microsoft.Storage/storageAccounts@2023-01-01' = {
  name: storageAccountName
  location: location
  // ...
}

// Outputs
output storageId string = storageAccount.id
```

### Resource Organization
1. Target scope declaration
2. Parameters (required first, optional after)
3. Variables
4. User-defined types
5. Resources
6. Modules
7. Outputs

## Naming Conventions

### Parameters
- Use camelCase for parameter names
- Include clear descriptions using @description decorator
- Group related parameters using objects
- Use strong typing with allowed values where applicable

```bicep
@description('The name of the environment')
@allowed(['dev', 'test', 'prod'])
param environmentName string

@description('Configuration for the storage account')
param storageConfig object = {
  sku: 'Standard_LRS'
  kind: 'StorageV2'
}
```

### Variables
- Use camelCase for variable names
- Use descriptive names that indicate purpose
- Prefix with temp_ for temporary calculations
- Include type information in name if not obvious

```bicep
var storageAccountName = '${prefix}stor${uniqueString(resourceGroup().id)}'
var tempCalculation = originalValue * 2
var isProduction = environmentName == 'prod'
```

### Resources
- Use camelCase for resource symbolic names
- Use descriptive names that indicate resource type and purpose
- Follow Azure naming restrictions for actual resource names
- Use consistent naming patterns across resources

```bicep
resource appServicePlan 'Microsoft.Web/serverfarms@2022-03-01' = {
  name: appServicePlanName
  location: location
  sku: {
    name: skuName
    tier: skuTier
  }
}
```

## Code Style

### Indentation and Spacing
- Use 2 spaces for indentation
- Add blank lines between logical sections
- Align property values for readability
- Use consistent spacing around operators

### Comments and Documentation
- Document all parameters using @description
- Add comments for complex logic or calculations
- Document dependencies and assumptions
- Use TODO comments for pending work
- Include reference links to documentation

```bicep
// TODO: Add support for private endpoints
@description('The SKU name for the storage account')
@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_RAGRS'
])
param skuName string
```

## Best Practices

### Parameter Design
- Use strong typing for all parameters
- Provide default values where appropriate
- Use allowed values to restrict input
- Use secure string for sensitive data
- Group related parameters into objects

### Resource Configuration
- Use resource dependencies explicitly
- Configure soft delete where available
- Enable diagnostics settings
- Configure proper access control
- Use managed identities where possible

### Security
- Never hardcode credentials
- Use Key Vault for secrets
- Enable encryption at rest
- Configure minimum TLS version
- Use private endpoints where appropriate

### Tagging
- Implement consistent tagging strategy
- Include mandatory tags: environment, owner, cost center
- Use variables for common tag values
- Apply tags at all resource levels

```bicep
var tags = {
  environment: environmentName
  owner: ownerEmail
  costCenter: costCenterCode
  lastDeployedBy: deployment().identity.principalId
}
```

### Error Handling
- Use conditions to handle optional resources
- Validate parameter values
- Handle deployment dependencies
- Use error messages in validations
- Implement proper rollback scenarios

### Performance
- Use copy loops efficiently
- Minimize dependencies between resources
- Use parallel deployments where possible
- Consider deployment time impacts
- Use appropriate SKUs and capacity

## Testing and Validation

### Pre-deployment Testing
- Validate parameter files
- Check resource names
- Verify dependencies
- Test with different parameter sets
- Use What-If operations

### Deployment Validation
- Monitor deployment progress
- Check resource provisioning
- Verify configurations
- Test resource access
- Validate outputs

## Resource Specific Guidelines

### Storage Accounts
```bicep
resource storageAccount 'Microsoft.Storage/storageAccounts@2023-01-01' = {
  name: storageAccountName
  location: location
  sku: {
    name: storageConfig.sku
  }
  kind: 'StorageV2'
  properties: {
    minimumTlsVersion: 'TLS1_2'
    supportsHttpsTrafficOnly: true
  }
}
```

### Key Vault
```bicep
resource keyVault 'Microsoft.KeyVault/vaults@2023-02-01' = {
  name: keyVaultName
  location: location
  properties: {
    sku: {
      family: 'A'
      name: 'standard'
    }
    tenantId: subscription().tenantId
    enableRbacAuthorization: true
    enableSoftDelete: true
    softDeleteRetentionInDays: 90
  }
}
```

### App Service
```bicep
resource appService 'Microsoft.Web/sites@2022-03-01' = {
  name: appServiceName
  location: location
  identity: {
    type: 'SystemAssigned'
  }
  properties: {
    serverFarmId: appServicePlan.id
    httpsOnly: true
    siteConfig: {
      minTlsVersion: '1.2'
    }
  }
}
```

## Modularization

### Module Design
- Create reusable modules for common patterns
- Keep modules focused and single-purpose
- Use clear parameter interfaces
- Return relevant outputs
- Document module usage

### Module Usage
```bicep
module storageModule './modules/storage.bicep' = {
  name: 'storageDeployment'
  params: {
    location: location
    environmentName: environmentName
    storageConfig: storageConfig
  }
}
```

## Deployment Guidelines

### Environment Management
- Use separate parameter files per environment
- Implement proper naming across environments
- Configure appropriate SKUs per environment
- Apply environment-specific settings
- Use conditions for environment-specific resources

### Rollout Strategy
- Use incremental deployments
- Implement proper dependencies
- Consider regional deployments
- Plan for rollback scenarios
- Document deployment steps
