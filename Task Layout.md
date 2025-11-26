# TASK-XXX: Task Title

**Section**: X. Section Name
**Subsection**: X.X
**Task ID**: TASK-XXX

## Description

Provide a clear, comprehensive description of what this task aims to accomplish. Explain the purpose, scope, and expected outcome. Include any relevant context about why this task is needed and how it fits into the larger project or phase.

**Reference Implementation** (if applicable): `path/to/reference/file` (lines X-Y)

## Current State

Describe the current state of the codebase or system relevant to this task. Include:

- What currently exists (or doesn't exist)
- Any dependencies that must be completed first
- Current limitations or issues
- Relevant findings from previous tasks or analysis
- Any constraints or considerations

**Important**: Note any prerequisites or execution timing requirements (e.g., "This task should be executed after Phase X conversion").

## Checklist

### Preparation and Setup

- [ ] Review relevant documentation and reference implementations
- [ ] Understand dependencies and prerequisites
- [ ] Set up any required tooling or environment
- [ ] Review related tasks and their status
- [ ] Identify any blockers or risks

### Implementation Steps

- [ ] Step 1: [Specific action to take]
  - [ ] Sub-step 1.1: [Detailed sub-action]
  - [ ] Sub-step 1.2: [Detailed sub-action]
- [ ] Step 2: [Specific action to take]
  - [ ] Sub-step 2.1: [Detailed sub-action]
- [ ] Step 3: [Specific action to take]

### Specific Requirements

- [ ] Requirement 1: [Detailed requirement with acceptance criteria]
- [ ] Requirement 2: [Detailed requirement with acceptance criteria]
- [ ] Requirement 3: [Detailed requirement with acceptance criteria]

### Error Handling and Edge Cases

- [ ] Handle error case 1: [Description]
- [ ] Handle error case 2: [Description]
- [ ] Handle edge case 1: [Description]
- [ ] Handle edge case 2: [Description]

### Testing

- [ ] Write unit tests for new functionality
- [ ] Write integration tests if applicable
- [ ] Test error handling paths
- [ ] Test edge cases
- [ ] Run full test suite: `npm test` (or equivalent)
- [ ] Run type checking: `npm run type-check` (if applicable)
- [ ] Run linting: `npm run lint` (if applicable)
- [ ] Verify test coverage: `npm run test:coverage` (if applicable)
- [ ] **DO NOT manually test by running the server** - use automated tests instead
- [ ] Ensure all affected functionality is covered by automated tests

### Documentation

- [ ] Update code comments and JSDoc/TSDoc as needed
- [ ] Update relevant documentation files
- [ ] Document any breaking changes (if applicable)
- [ ] Update architecture documentation if structural changes were made
- [ ] Document any patterns or conventions established

### Verification

- [ ] Verify all requirements are met
- [ ] Verify no regressions were introduced
- [ ] Verify code quality standards are met
- [ ] Review code for best practices
- [ ] Ensure proper error handling is in place
- [ ] Verify performance considerations (if applicable)

## Notes

- This task is part of [Phase/Section Name]
- **Execution Timing**: Note when this task should be executed relative to other tasks
- **Dependencies**: List any tasks that must be completed before this one
- **Important Considerations**: 
  - Note any critical implementation details
  - Note any gotchas or common pitfalls
  - Note any performance or security considerations
- **Task Independence**: Note whether this task can be completed independently or requires coordination
- **Current State**: Note the current state of relevant code or systems

## Related Tasks

- Previous: TASK-XXX-1
- Next: TASK-XXX+1
- Dependencies:
  - TASK-DEP-1: [Description of dependency]
  - TASK-DEP-2: [Description of dependency]
- Related:
  - TASK-REL-1: [Description of relationship]
  - TASK-REL-2: [Description of relationship]

## Definition of Done

This document defines the criteria for task completion. The review agent uses these definitions to evaluate whether a task has been completed successfully.

### 1. CODE/FILE WRITING TASKS

**Description**: Tasks that involve writing, creating, modifying, or implementing SOURCE CODE FILES that need to be committed to git.

**Definition of Done**: "A Pull Request was created OR code was pushed to origin with the task complete"

**Examples**:
- Creating new source code files
- Modifying existing source code files
- Implementing features, functions, classes, modules
- Writing tests, specs
- Refactoring code
- Fixing bugs in source code

### 2. SYSTEM/ENVIRONMENT OPERATION TASKS

**Description**: Tasks involving installing dependencies, running builds, installing packages, running migrations, executing install scripts, etc.

**Definition of Done**: "The required operation must complete successfully with no errors, and the expected artifacts must be created. If any part of the operation fails, the task is NOT complete."

**Important Notes**:
- Installing dependencies requires packages to actually be installed successfully
- Updating package.json is NOT enough
- If the output mentions environmental issues, errors, warnings, or failed operations, the task is NOT complete

**Examples**:
- Installing npm packages, pip packages, gem dependencies
- Running database migrations
- Building/compiling projects
- Setting up development environments
- Running install scripts

### 3. DOCUMENTATION TASKS

**Description**: Tasks that involve creating or updating documentation files.

**Definition of Done**: "Documentation files are created or updated with accurate, complete information and committed to git"

**Examples**:
- Creating README files
- Updating API documentation
- Writing architecture documentation
- Creating user guides
- Updating code comments

### 4. TESTING TASKS

**Description**: Tasks that involve writing or updating tests.

**Definition of Done**: "Tests are written, all tests pass, and test coverage meets project requirements (if applicable)"

**Examples**:
- Writing unit tests
- Writing integration tests
- Writing E2E tests
- Updating test fixtures
- Improving test coverage

### 5. CONFIGURATION TASKS

**Description**: Tasks that involve configuring systems, tools, or environments.

**Definition of Done**: "Configuration is complete, verified to work correctly, and committed to git"

**Examples**:
- Setting up CI/CD pipelines
- Configuring linters or formatters
- Setting up database schemas
- Configuring deployment environments
- Setting up monitoring or logging

---

## Template Usage Notes

When creating a new task using this template:

1. **Replace placeholders**:
   - `TASK-XXX` with actual task ID
   - `X. Section Name` with actual section information
   - All checklist items with specific, actionable items
   - All notes with relevant information

2. **Customize sections**:
   - Add or remove checklist subsections as needed
   - Expand on specific requirements based on task complexity
   - Include relevant code examples or snippets if helpful

3. **Ensure completeness**:
   - All dependencies are clearly listed
   - All acceptance criteria are specified
   - All testing requirements are defined
   - Definition of Done is appropriate for the task type

4. **Keep it actionable**:
   - Checklist items should be specific and verifiable
   - Avoid vague or ambiguous requirements
   - Include file paths, function names, and specific implementation details where relevant


