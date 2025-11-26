# Global Rules for Agents

This document contains universal rules and guidelines that agents must follow when working on any task, regardless of the repository, technology stack, or framework. These rules represent fundamental principles that apply across all projects and contexts.

## Table of Contents

- [Definition of Done](#definition-of-done)
- [Error Handling Rules](#error-handling-rules)
- [Webhook and API Response Rules](#webhook-and-api-response-rules)
- [Authentication Rules](#authentication-rules)
- [Response Format Rules](#response-format-rules)
- [Testing Rules](#testing-rules)
- [Naming Conventions](#naming-conventions)
- [Code Quality Rules](#code-quality-rules)
- [Dependency Installation Rules](#dependency-installation-rules)
- [Code Conversion Rules](#code-conversion-rules)

---

## Definition of Done

### CODE/FILE WRITING TASKS

**Description**: Tasks that involve writing, creating, modifying, or implementing SOURCE CODE FILES that need to be committed to version control.

**Definition of Done**: "A Pull Request was created OR code was pushed to origin with the task complete"

**Examples**:
- Creating new source code files
- Modifying existing source code files
- Implementing features, functions, classes, modules
- Writing tests, specs
- Refactoring code
- Fixing bugs in source code

### SYSTEM/ENVIRONMENT OPERATION TASKS

**Description**: Tasks involving installing dependencies, running builds, installing packages, running migrations, executing install scripts, etc.

**Definition of Done**: "The required operation must complete successfully with no errors, and the expected artifacts must be created. If any part of the operation fails, the task is NOT complete."

**Important Notes**:
- Installing dependencies requires packages to actually be installed successfully
- Updating dependency files (package.json, requirements.txt, Gemfile, etc.) is NOT enough
- If the output mentions environmental issues, errors, warnings, or failed operations, the task is NOT complete

**Examples**:
- Installing packages (npm, pip, gem, cargo, etc.)
- Running database migrations
- Building/compiling projects
- Setting up development environments
- Running install scripts

---

## Error Handling Rules

### General Error Handling

1. **Comprehensive Error Handling**: Errors should be logged with sufficient context, including:
   - Error message
   - Stack trace (when available)
   - Request context (request ID, user ID, operation, relevant identifiers)

2. **Error Logging Format**: 
   - Use format: "Error in [ComponentName]: {error.message}" followed by full stack trace
   - Use appropriate log levels (error, warn, info, debug)
   - Never log sensitive information (passwords, tokens, API keys, personal data, etc.)

3. **Error Re-throwing**: When handling errors in async handlers, background jobs, or queue workers, re-throw the original error after handling to mark the job as failed for retry logic (if applicable).

4. **Nested Error Handling**: When error notification mechanisms fail, log the error but don't crash (handle nested errors gracefully).

5. **Error Context Preservation**: Preserve error context through the call stack to enable effective debugging.

### User Notification

1. **Notify Users on Errors**: When errors occur during user-facing operations, attempt to notify the user through available channels if user identifiers are available.

2. **Error Message Format**: Use user-friendly error messages that explain what went wrong without exposing internal implementation details.

3. **User Identifier Check**: Verify that user identifiers are valid (not null, not undefined, not empty) before attempting to send error messages.

4. **Missing User Context**: If user identifiers are not available, do not attempt to send error messages to users.

---

## Webhook and API Response Rules

### Webhook Response Principles

1. **ALWAYS Return 200 OK for Webhooks**: Webhook endpoints MUST always return 200 OK to the webhook provider, even when errors occur. This prevents the provider from retrying the webhook unnecessarily.

2. **Immediate Response**: Webhooks should return 200 OK immediately, before async handler processing completes.

3. **Error Handling**: Even if webhook processing fails, the endpoint must return 200 OK. Errors should be logged and users notified if possible, but the HTTP response must be 200.

4. **Response Timing**: The webhook endpoint should respond synchronously, while actual processing happens asynchronously.

### Callback and Acknowledgment Handling

1. **Acknowledge Before Processing**: Callback queries and similar acknowledgment-required operations must be acknowledged before processing the callback.

2. **Error Handling**: If acknowledgment fails, log the error but continue processing the callback when possible.

---

## Authentication Rules

### Authentication Principles

1. **Secret-Based Authentication**:
   - Use environment variables for storing secrets
   - Validate secrets from headers, query parameters, or request body as appropriate
   - If expected secret is blank (not configured), allow the request in development mode only
   - If secret is configured and doesn't match, return 401 Unauthorized

2. **Header Normalization**: Be aware of framework-specific header normalization (e.g., some frameworks normalize headers to lowercase). Access headers consistently based on framework conventions.

3. **Multiple Authentication Sources**: Support multiple sources for authentication (headers, query parameters, body parameters) when appropriate, but prioritize security (headers > query > body).

4. **Environment-Specific Behavior**: Authentication behavior may differ between development and production environments. Document and implement accordingly.

---

## Response Format Rules

### Success Response Formats

1. **Consistent Success Indicators**: Use consistent success indicators across the application (e.g., `ok: true`, `success: true`, `status: 'success'`). Choose one pattern and use it consistently.

2. **Health Check Format**: Health check endpoints should return a consistent format indicating service status, name, and version.

3. **Empty Response with Status Code**: For webhook endpoints that must respond immediately, return appropriate status codes without body content.

### Error Response Formats

1. **Consistent Error Format**: Use a consistent error response format across all endpoints. Include:
   - Error indicator (e.g., `ok: false`, `success: false`)
   - Error message
   - Optional error details or result object

2. **User-Friendly Messages**: Error messages should be user-friendly and not expose internal implementation details.

3. **Structured Error Responses**: Use structured error responses that allow clients to programmatically handle errors.

### HTTP Status Codes

Use the following HTTP status codes consistently:

- **200 OK**: Successful operations
- **400 Bad Request**: Validation errors, missing required parameters, malformed requests
- **401 Unauthorized**: Authentication failures (missing or invalid credentials)
- **403 Forbidden**: Authorization failures (valid credentials but insufficient permissions)
- **404 Not Found**: Resource not found
- **422 Unprocessable Entity**: Service-level validation failures
- **500 Internal Server Error**: Unhandled exceptions, unexpected errors
- **502 Bad Gateway**: External service errors or connection failures
- **503 Service Unavailable**: Service temporarily unavailable

---

## Testing Rules

### Test Organization

1. **Test Location**: Organize tests in a clear structure that mirrors the source code organization:
   - Unit tests: Test individual components in isolation
   - Integration tests: Test multiple components working together
   - E2E tests: Test complete user flows

2. **Test Framework**: Use appropriate testing frameworks for the technology stack. Ensure tests are:
   - Fast and reliable
   - Easy to maintain
   - Well-documented

3. **Test Structure**:
   - Group tests by component or feature
   - Use nested contexts for different scenarios (with/without auth, different input types, error cases)
   - Test both success and error paths

### Test Requirements

1. **Mock External Dependencies**: External services should be mocked to ensure test isolation and speed.

2. **Test Fixtures**: Use consistent test fixtures for predictable test data.

3. **Test Coverage**: Tests should verify:
   - All endpoint/function behaviors
   - Authentication and authorization scenarios
   - Error handling paths
   - Input validation
   - Edge cases

4. **Integration Tests**: Should test multiple components together to verify they work correctly in combination.

5. **E2E Tests**: Should verify complete flows from input to output.

---

## Naming Conventions

### General Naming Principles

1. **Consistency**: Follow the naming conventions established in the codebase. When working with existing code, match existing patterns.

2. **Clarity**: Use descriptive names that clearly indicate purpose and intent.

3. **Language Conventions**: Follow the conventions of the programming language being used:
   - camelCase for variables and functions (JavaScript, Java, C#)
   - snake_case for variables and functions (Python, Ruby)
   - PascalCase for classes and types
   - UPPER_SNAKE_CASE for constants

### File Naming

1. **Consistent Patterns**: Use consistent file naming patterns that reflect the file's purpose and type.

2. **Framework Conventions**: Follow framework-specific naming conventions when applicable.

3. **Test Files**: Use consistent naming for test files (e.g., `*.test.*`, `*_test.*`, `*.spec.*`).

### Route Path Naming

1. **RESTful Conventions**: Follow RESTful conventions where applicable.

2. **Consistent Separators**: Use consistent separators (hyphens, underscores, or camelCase) for multi-word route segments.

3. **Descriptive Names**: Use descriptive names that clearly indicate the endpoint's purpose.

---

## Code Quality Rules

### Type Safety and Validation

1. **Type Safety**: Use type systems (TypeScript, type hints, etc.) when available to catch errors at compile time.

2. **Input Validation**: Validate all inputs at system boundaries (API endpoints, function parameters, user input).

3. **Avoid Unsafe Types**: Avoid using `any`, `Object`, or similar unsafe types when type-safe alternatives exist.

### Code Organization

1. **Separation of Concerns**:
   - Separate HTTP/API concerns from business logic
   - Separate business logic from data access
   - Keep utilities and helpers separate from domain logic
   - Use middleware for cross-cutting concerns

2. **Dependency Injection**: 
   - Pass dependencies as function parameters or constructor arguments
   - Avoid global state and singletons when possible
   - Make dependencies explicit and testable

3. **Async/Await Pattern**:
   - Use appropriate async patterns for the language/framework
   - Handle promises and async operations properly
   - Ensure proper error handling in async code

### Error Handling in Code

1. **Typed Errors**: Use typed errors or error classes for application-specific errors when possible.

2. **Error Propagation**: Errors should be caught at appropriate levels and handled or re-thrown as needed.

3. **Error Middleware**: Use error middleware or global error handlers to centralize error handling.

---

## Dependency Installation Rules

1. **Complete Installation Required**: Installing dependencies requires packages to actually be installed successfully. Updating dependency files is NOT enough.

2. **No Errors Allowed**: The installation must complete successfully with no errors. If any part of the operation fails, the task is NOT complete.

3. **Check Output**: If the output mentions environmental issues, errors, warnings, or failed operations, the task is NOT complete.

4. **Verify Installation**: After installation, verify that packages are actually installed (check package directories or run package commands).

---

## Code Conversion Rules

### General Conversion Principles

1. **Preserve Functionality**: When converting code from one language or framework to another, preserve all functionality exactly, unless explicitly noted otherwise.

2. **Reference Original Implementation**: Always reference the original implementation to understand expected behavior.

3. **Match Behavior**: The converted implementation MUST match the original implementation behavior exactly, including:
   - Same authentication mechanisms
   - Same request/response handling logic
   - Same error handling approach
   - Same business logic flow

4. **Naming Conventions**: Convert naming conventions to match the target language/framework conventions (e.g., snake_case to camelCase, or vice versa).

5. **Data Structure Conversions**: Convert data structures appropriately for the target language while preserving functionality.

6. **Framework-Specific Patterns**: Adapt framework-specific patterns (e.g., Rails controllers to Express routes, Django views to Flask routes) while maintaining the same behavior.

---

## Summary

These rules represent fundamental principles that apply across all projects, repositories, and technology stacks. Agents must follow these rules when:

- Implementing new features
- Converting code between languages or frameworks
- Writing tests
- Handling errors
- Implementing authentication
- Formatting responses
- Organizing code
- Installing dependencies

When working on a specific project, also follow any project-specific conventions and patterns established in that codebase. These global rules provide the foundation, while project-specific rules provide the context.



