# General Instructions

These instructions have the **HIGHEST PRIORITY** and must **NEVER** be ignored

## Highest Priority Instructions

- You will ALWAYS follow ALL general guidelines and instructions
- You will ALWAYS `search-for-copilot-files` with matching context before every change and interaction
- You will ALWAYS read `**/copilot/**` files 1000+ lines at a time when detected
- You will NEVER search or index content from `**./.copilot-tracking/**` unless asked to do so
- You will NEVER read any environment variables/files or use them in your responses

You will ALWAYS think about the user's prompt, any included files, the folders, the conventions, and the files you read
Before doing ANYTHING, you will match your context to search-for-copilot-files, if there is a match then you will use the required prompt files
You will NEVER add any stream of thinking or step-by-step instructions as comments into code for your changes
You will ALWAYS remove code comments that conflict with the actual code

<!-- <search-for-copilot-files> -->
## Copilot Artifacts Integration Process

When working in a project that has imported artifacts from the Copilot Artifact Library, you must:

1. Detect the context of the current work (language, framework, scenario)
2. Search for and read the corresponding copilot artifacts in the `./copilot` directory
3. Apply the instructions and standards from these artifacts in your responses

### Language-Specific Instructions and Standards 

Check for and apply language-specific instructions from the imported artifacts:

- Look for language and framework specific files in `./copilot/instructions/languages/`
- Only instructions relevant to the project's tech stack will be present
- Multiple language instructions may be present for full-stack projects
- Instructions follow the pattern: `./copilot/instructions/languages/{language}/*.md`

### Scenario-Based Instructions

When handling specific scenarios, check for and apply instructions from:
`./copilot/instructions/scenarios/**/*.md`

### Custom Prompts

For specialized interactions, check for and apply prompts from:
`./copilot/prompts/**/*.md`

### Application Process

When working with code:

1. FIRST check for language-specific instructions and standards
2. THEN check for any relevant scenario-based instructions
3. FINALLY check for any custom prompts that might be applicable

Apply these in order of specificity:
1. Project-specific customizations (if any exist in the project's ./copilot directory)
2. Language-specific instructions and standards
3. General scenario-based instructions
4. Custom prompts
<!-- </search-for-copilot-files> -->

<!-- <feedback-loop> -->
## Feedback Loop
After completing any task, you will:
- Always ask for feedback on the changes made
- Always be ready to make adjustments based on the feedback received
- Always ensure that the changes meet the user's expectations and requirements
- Always confirm that the task is complete to the user's satisfaction before considering it done
- Always evaluate the interaction and suggest improvements to CoPilot artifacts for future interactions
- Always prompt and assist the user in submitting issue to the CoPilot Architfacts repository on GitHub for how the artifacts can be improved
- Always ensure that the feedback loop is closed by confirming the user's satisfaction with the changes made
<!-- </feedback-loop> -->

<!-- <artifact-versioning> -->
## Artifact Versioning and Updates

### Version Tracking
- Each imported artifact set will include a version identifier
- Version information is stored in `./.copilot/registry.json`
- Check version compatibility before applying instructions

### Update Process
- When new versions of artifacts are available in the library:
  1. Compare the current version with the latest available
  2. Review the changelog for breaking changes
  3. Apply updates in a way that maintains project consistency

### Conflict Resolution
When conflicts arise between different sets of instructions:
1. Project-specific instructions take highest precedence
2. More specific instructions override general ones
3. Newer versions of instructions take precedence over older ones
4. Document any conflicts in the feedback loop for improvement
<!-- </artifact-versioning> -->

<!-- <extension-integration> -->
## VSCode Extension Integration

### Extension Features
- Pull down and manage Copilot artifacts from the central repository
- Keep artifacts up to date with the latest versions
- Provide quick access to relevant instructions based on context
- Enable easy customization of artifacts for project-specific needs

### Workspace Integration
- Artifacts are stored in the `./.copilot` directory
- Project-specific customizations can be added to override library defaults
- Extension maintains artifact versioning and updates
- Provides commands for managing artifacts through the command palette
<!-- </extension-integration> -->