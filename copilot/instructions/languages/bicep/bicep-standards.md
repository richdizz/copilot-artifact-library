---
applyTo: '**/*.bicep'
---

# Bicep Code Standards

## 1. Naming Standards

### Resource Names
- Use lowercase alphanumeric characters
- Include environment indicator
- Follow Azure naming restrictions
- Use consistent abbreviations
- Include purpose in name

### File Names
- Use kebab-case
- Indicate resource type
- Include purpose
- Use standard extensions
- Separate parameter files

### Variable & Parameter Names
- Use camelCase
- Be descriptive
- Include type if not obvious
- Group related items
- Follow consistent patterns

## 2. Code Structure Standards

### File Organization
```bicep
// 1. Target scope
targetScope = 'resourceGroup'

// 2. Parameters (required first)
@description('Required parameters')
param location string
param environmentName string

// 3. Parameters (optional with defaults)
@description('Optional parameters')
param skuName string = 'Standard_LRS'

// 4. Variables
var storageAccountName = '${prefix}stor${uniqueString(resourceGroup().id)}'

// 5. Resources
resource storageAccount '...' = { }

// 6. Modules
module appService '...' = { }

// 7. Outputs
output storageId string = storageAccount.id
```

### Resource Structure
```bicep
resource resourceName 'provider/type@version' = {
  name: resourceName
  location: location
  tags: tags
  properties: {
    // Required properties first
    // Optional properties after
  }
}
```

### Module Structure
```bicep
module moduleName './path/to/module.bicep' = {
  name: deploymentName
  params: {
    // Required parameters first
    // Optional parameters after
  }
}
```

## 3. Documentation Standards

### Parameter Documentation
```bicep
@description('Clear description of the parameter purpose')
@allowed([
  'value1'
  'value2'
])
param parameterName string
```

### File Header
```bicep
// Description: Purpose of this template
// Author: Name
// Last Modified: Date
// Dependencies: List any dependencies
```

### Code Comments
- Add context for complex logic
- Explain non-obvious choices
- Document assumptions
- Include reference links
- Mark temporary code

## 4. Security Standards

### Authentication & Authorization
- Use managed identities
- Implement RBAC
- Avoid shared credentials
- Use Key Vault references
- Enable audit logging

### Network Security
- Use private endpoints
- Enable firewalls
- Configure allowed IPs
- Use service endpoints
- Enable HTTPS only

### Data Protection
- Enable encryption
- Use latest TLS version
- Enable soft delete
- Configure backup
- Implement monitoring

## 5. Performance Standards

### Resource Configuration
- Right-size resources
- Use appropriate SKUs
- Configure auto-scaling
- Optimize for cost
- Consider regions

### Deployment Optimization
- Use parallel deployment
- Minimize dependencies
- Use copy loops efficiently
- Consider timeouts
- Handle long-running operations

## 6. Testing Standards

### Pre-deployment
- Validate parameters
- Check dependencies
- Use What-If operation
- Test with different values
- Verify naming

### Post-deployment
- Verify provisioning
- Check configurations
- Test connectivity
- Validate security
- Monitor performance

## 7. Tag Standards

### Required Tags
```bicep
var tags = {
  environment: environmentName
  application: applicationName
  owner: ownerEmail
  costCenter: costCenterCode
  createdBy: deployment().identity.principalId
  createdOn: utcNow()
}
```

### Tag Usage
- Apply consistently
- Use at all levels
- Update during changes
- Include mandatory tags
- Use for cost allocation

## 8. Error Handling Standards

### Parameter Validation
```bicep
@allowed([
  'dev'
  'test'
  'prod'
])
param environmentName string

@minLength(3)
@maxLength(24)
param storageAccountName string
```

### Deployment Validation
- Check dependencies
- Validate outputs
- Handle failures
- Implement rollback
- Log errors

## 9. Module Standards

### Module Interface
- Clear parameter names
- Strong type checking
- Meaningful defaults
- Relevant outputs
- Proper documentation

### Module Implementation
- Single responsibility
- Minimal dependencies
- Consistent style
- Error handling
- Performance consideration

## 10. Environment Standards

### Configuration
- Environment-specific params
- Appropriate SKUs
- Security levels
- Monitoring setup
- Backup configuration

### Deployment
- Consistent process
- Environment isolation
- Progressive rollout
- Rollback plan
- Monitoring strategy
