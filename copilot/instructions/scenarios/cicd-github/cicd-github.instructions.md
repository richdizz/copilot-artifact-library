# GitHub Actions CI/CD Instructions

## Workflow Structure Guidelines

### General Principles
- Use YAML-based workflow files in `.github/workflows`
- Implement reusable workflows for common patterns
- Follow the DRY (Don't Repeat Yourself) principle using composite actions
- Implement proper environment isolation
- Use GitHub Secrets and Variables for configuration
- Implement proper security scanning and testing stages

### Naming Conventions
- Workflow files should be descriptive: `{purpose}-{environment}.yml`
- Job names should clearly indicate their purpose
- Step names should be clear and action-oriented
- Custom action names should follow: `{org}/{repo}/{action-name}@{version}`

### Required Workflow Components

#### Secrets and Variables
- Repository secrets for sensitive data
- Environment secrets for environment-specific values
- GitHub variables for shared configuration
- Action variables for workflow-specific settings

#### Triggers
- Push events with proper path filters
- Pull request events with appropriate triggers
- Scheduled workflows using cron syntax
- Manual workflow dispatch
- Repository dispatch events

#### Jobs and Steps
1. **Build Job**
   - Checkout code
   - Setup runtime environment
   - Cache dependencies
   - Install dependencies
   - Build process
   - Unit testing
   - Code coverage
   - Static analysis
   - Artifact upload

2. **Test Job**
   - Integration testing
   - Security scanning
   - Compliance checks
   - Performance testing
   - Test reports generation

3. **Deploy Job**
   - Environment configuration
   - Deployment steps
   - Smoke tests
   - Health checks
   - Rollback procedures

### Security Best Practices
- Use GITHUB_TOKEN with minimum required permissions
- Implement secret scanning with GitHub Advanced Security
- Enable dependency scanning
- Use GitHub-hosted runners or secure self-hosted runners
- Implement environment protection rules
- Use OpenID Connect for cloud deployments

### Environment Management
- Define environments in repository settings
- Set environment protection rules
- Implement required reviewers
- Use deployment branches
- Set up deployment gates

### Monitoring and Logging
- Enable workflow analytics
- Use workflow annotations
- Set up status badges
- Configure notifications
- Track deployment frequency

### Quality Gates
- Branch protection rules
- Required status checks
- Code coverage thresholds
- Security scan results
- Performance benchmarks

## Reusable Workflows

### Composite Actions
- Create reusable action steps
- Version control your actions
- Document inputs and outputs
- Implement error handling

### Workflow Templates
- Build templates per technology
- Deployment templates per target
- Quality check templates
- Security scan templates

## Workflow Configuration

### Event Types
- push
- pull_request
- schedule
- workflow_dispatch
- repository_dispatch

### Runner Selection
- GitHub-hosted runners
- Self-hosted runners
- Labels and groups
- Runner scaling

## Error Handling

### Workflow Failures
- Use continue-on-error where appropriate
- Implement proper error catching
- Set up failure notifications
- Maintain error logs

### Recovery Procedures
- Document rollback steps
- Implement automated rollbacks
- Track deployment history
- Set up recovery points

## Compliance and Governance

### Audit Requirements
- Workflow run logs
- Deployment history
- Configuration changes
- Access modifications

### Policy Enforcement
- Branch protection
- Required reviews
- Status checks
- Security policies

## Testing Strategy

### Automated Tests
- Unit tests in build job
- Integration tests in separate job
- End-to-end tests post-deployment
- Performance tests in matrix strategy

### Matrix Testing
- Multiple OS testing
- Cross-browser testing
- Multiple runtime versions
- Database versions

### Manual Interventions
- Environment approvals
- Security reviews
- Compliance checks
- Performance validation

## GitHub Features Integration

### Actions
- GitHub Marketplace actions
- Custom actions
- Composite actions
- Container actions

### Packages
- GitHub Packages registry
- Container registry
- npm packages
- Maven packages

### Security
- Code scanning
- Secret scanning
- Dependency review
- Security advisories

### Project Integration
- Project boards
- Issue management
- Pull request automation
- Release management
