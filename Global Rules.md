# Global Rules for Agents

This document contains repeating rules and guidelines that agents must follow when working on tasks in the telegram-receiver application. These rules are extracted from task files across all phases and represent consistent patterns and requirements.

## Table of Contents

- [Definition of Done](#definition-of-done)
- [Rails Conversion Rules](#rails-conversion-rules)
- [Error Handling Rules](#error-handling-rules)
- [Webhook Response Rules](#webhook-response-rules)
- [Authentication Rules](#authentication-rules)
- [Response Format Rules](#response-format-rules)
- [Testing Rules](#testing-rules)
- [Naming Conventions](#naming-conventions)
- [Code Quality Rules](#code-quality-rules)
- [Dependency Installation Rules](#dependency-installation-rules)

---

## Definition of Done

### CODE/FILE WRITING TASKS

**Description**: Tasks that involve writing, creating, modifying, or implementing SOURCE CODE FILES that need to be committed to git.

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
- Updating package.json is NOT enough
- If the output mentions environmental issues, errors, warnings, or failed operations, the task is NOT complete

**Examples**:
- Installing npm packages, pip packages, gem dependencies
- Running database migrations
- Building/compiling projects
- Setting up development environments
- Running install scripts

---

## Rails Conversion Rules

### General Conversion Principles

1. **Match Rails Implementation**: When converting from Rails, the TypeScript/Node.js implementation MUST match the Rails implementation behavior exactly, unless explicitly noted otherwise.

2. **Reference Rails Files**: Always reference the Rails implementation files in `jarek-va/` to understand the expected behavior.

3. **Preserve Functionality**: The conversion must preserve all functionality from the Rails version, including:
   - Same webhook authentication mechanism
   - Same request forwarding logic
   - Same callback handling flow
   - Same error handling approach
   - Same local command processing

4. **Method Name Conversions**:
   - Rails `perform` method → TypeScript `process` method (for job handlers)
   - Rails snake_case methods → TypeScript camelCase methods (e.g., `set_webhook` → `setWebhook`)
   - Rails controller actions → TypeScript controller methods with camelCase

5. **Data Structure Conversions**:
   - Rails `with_indifferent_access` → Normalize object structure to allow both string and number key access
   - Rails Hash → TypeScript object/interface
   - Rails params → TypeScript `req.body` or `req.query`

6. **Update Parsing**: Updates can come as either JSON strings or objects. Must parse if string using `JSON.parse()`, then normalize the object structure.

---

## Error Handling Rules

### General Error Handling

1. **Comprehensive Error Handling**: Errors should be logged with sufficient context, including:
   - Error message
   - Stack trace
   - Request context (request ID, user ID, operation)

2. **Error Logging Format**: 
   - Use format: "Error in [ComponentName]: {error.message}" followed by full stack trace
   - Use appropriate log levels (error, warn, info)
   - Never log sensitive information (passwords, tokens, etc.)

3. **Error Re-throwing**: When handling errors in async handlers/jobs, must re-throw the original error after handling to mark the job as failed for retry logic.

4. **Nested Error Handling**: When sending error messages fails, log the error but don't crash (handle nested errors gracefully).

### User Notification

1. **Notify Users on Errors**: When errors occur during user-facing operations, attempt to notify the user via Telegram if `chat_id` is available.

2. **Error Message Format**: Use format: "Sorry, I encountered an error processing your message: {error.message}"

3. **Chat ID Check**: Use truthy check (not null, not undefined, not empty string) equivalent to Rails' `.present?` method before attempting to send error messages.

4. **Missing Chat ID**: If `chat_id` is not available, do not attempt to send error message.

---

## Webhook Response Rules

### Telegram Webhook Responses

1. **ALWAYS Return 200 OK**: Webhook endpoints MUST always return 200 OK to Telegram, even when errors occur. This prevents Telegram from retrying the webhook.

2. **Immediate Response**: Webhook should return 200 OK immediately, before async handler processing completes.

3. **Error Handling**: Even if webhook processing fails, the endpoint must return 200 OK. Errors should be logged and users notified if possible, but the HTTP response must be 200.

4. **Response Timing**: The webhook endpoint should respond synchronously, while actual processing happens asynchronously.

### Callback Query Handling

1. **Answer Before Processing**: Callback queries must be answered before processing the callback.

2. **Error Handling**: If answering the callback query fails, log the error but continue processing the callback.

---

## Authentication Rules

### Webhook Authentication

1. **Telegram Webhook Authentication**:
   - Header: `X-Telegram-Bot-Api-Secret-Token`
   - Environment variable: `TELEGRAM_WEBHOOK_SECRET`
   - If expected secret is blank (not configured), allow the request (development mode)
   - If secret is configured and doesn't match, return 401 Unauthorized

2. **Admin Authentication**:
   - Header: `X-Admin-Secret`
   - Alternative sources: `HTTP_X_ADMIN_SECRET` env variable, `admin_secret` query parameter, `admin_secret` body parameter
   - Environment variable: `WEBHOOK_SECRET`
   - Must match exactly, return 401 Unauthorized if mismatch

3. **Cursor Runner Webhook Authentication**:
   - Header: `X-Webhook-Secret` or `X-Cursor-Runner-Secret`
   - Validate against configured secret

4. **Express Header Normalization**: Express normalizes header names to lowercase, so use lowercase when accessing headers (e.g., `req.headers['x-admin-secret']`).

---

## Response Format Rules

### Success Response Formats

1. **Standard Success Format** (`{ ok: true, ... }`):
   - Used by most controllers for standard API responses
   - Used by: `TelegramController`, `AgentToolsController`

2. **Service Response Format** (`{ success: true, ... }`):
   - Used when proxying responses directly from external services
   - Used by: `CursorRunnerController`

3. **Health Check Format** (`{ status: 'healthy', service: ..., version: ... }`):
   - Used for health check endpoints
   - Status value MUST be `"healthy"` (not "ok")
   - Service name from `process.env.APP_NAME` or default `'Virtual Assistant API'`
   - Version from `process.env.APP_VERSION` or default `'1.0.0'`

4. **Callback Acknowledgment Format** (`{ received: true, ... }`):
   - Used for webhook callback acknowledgments
   - Used by: `CursorRunnerCallbackController`

5. **Empty Response with Status Code**:
   - Used for webhook endpoints that must respond immediately
   - Return `res.sendStatus(200)` or `res.status(200).end()`

### Error Response Formats

1. **Global Error Handler Format**:
   ```json
   {
     "ok": false,
     "say": "Sorry, I encountered an error processing your request.",
     "result": {
       "error": "Error message here"
     }
   }
   ```

2. **Controller-Level Error Format**:
   ```json
   {
     "ok": false,
     "error": "Error message here"
   }
   ```

3. **Service-Level Error Format**:
   ```json
   {
     "success": false,
     "error": "Error message here"
   }
   ```

4. **Simple Error Format** (for authentication/validation):
   ```json
   {
     "error": "Unauthorized"
   }
   ```

### HTTP Status Codes

Use the following HTTP status codes consistently:

- **200 OK**: Successful operations
- **400 Bad Request**: Validation errors, missing required parameters
- **401 Unauthorized**: Authentication failures (missing or invalid credentials)
- **422 Unprocessable Entity**: Service-level validation failures (e.g., invalid repository name)
- **500 Internal Server Error**: Unhandled exceptions, unexpected errors
- **502 Bad Gateway**: External service errors (e.g., CursorRunnerService connection failures)

---

## Testing Rules

### Test Organization

1. **Test Location**:
   - Unit tests: `tests/unit/` (mirrors `src/` structure)
   - Integration tests: `tests/integration/api/` for API endpoints
   - E2E tests: `tests/e2e/` for end-to-end tests

2. **Test Framework**:
   - Use Jest with Supertest for HTTP endpoint testing
   - Use Playwright for E2E tests (when applicable)
   - E2E tests for backend API should use Supertest (not Playwright)

3. **Test Structure**:
   - Group tests by endpoint or component
   - Use nested contexts for different scenarios (with/without auth, different update types, error cases)
   - Test both success and error paths

### Test Requirements

1. **Mock External Dependencies**: External services should be mocked to ensure test isolation and speed.

2. **Test Fixtures**: Use existing fixtures from `tests/fixtures/` for consistent test data.

3. **Test Coverage**: Tests should verify:
   - All endpoint behaviors
   - Authentication scenarios
   - Error handling paths
   - Update type variations (message, edited_message, callback_query)
   - Async handler execution

4. **Integration Tests**: Should test multiple components together (e.g., controller + async handler execution).

5. **E2E Tests**: Should verify the complete flow from webhook receipt to Telegram response.

---

## Naming Conventions

### File Naming

1. **Controllers**: `*.controller.ts` (e.g., `health.controller.ts`)
2. **Routes**: `*.routes.ts` (e.g., `health.routes.ts`)
3. **Middleware**: `*.middleware.ts` (e.g., `error-handler.middleware.ts`)
4. **Services**: `*.service.ts` (e.g., `telegram.service.ts`)
5. **Models**: `*.model.ts` or singular noun (e.g., `user.model.ts`)
6. **Types**: `*.ts` with descriptive names (e.g., `telegram.ts`, `cursor-runner.ts`)
7. **Utils**: `*.ts` with descriptive names (e.g., `logger.ts`)
8. **Test Files**: `*.test.ts` for unit/integration tests, `*.spec.ts` for E2E tests

### Code Naming

1. **Use kebab-case** for file names (e.g., `error-handler.middleware.ts`)
2. **Use camelCase** for TypeScript identifiers (variables, functions, methods)
3. **Use PascalCase** for class names and type names
4. **Use UPPER_SNAKE_CASE** for constants and environment variables
5. **Use snake_case** for route paths (e.g., `set_webhook`, `webhook_info`)
6. **Use camelCase** for method names (TypeScript convention)

### Route Path Naming

1. **Use snake_case** for route paths (e.g., `set_webhook`, `webhook_info`)
2. **Use kebab-case** for multi-word route segments (e.g., `/cursor-runner`, `/agent-tools`)
3. **Use descriptive names** that clearly indicate the endpoint's purpose
4. **Follow RESTful conventions** where applicable

---

## Code Quality Rules

### TypeScript Rules

1. **Type Safety**: Use TypeScript types and interfaces for all data structures
2. **Avoid `any` Types**: Use proper generic constraints instead of `any` types
3. **Optional Fields**: Mark optional fields with `?` in TypeScript interfaces
4. **Required Fields**: Do not mark required fields with `?`

### Code Organization

1. **Separation of Concerns**:
   - Controllers: HTTP concerns only (delegate to services)
   - Services: Business logic and external integrations
   - Routes: Only define URL patterns and map to controllers
   - Middleware: Cross-cutting concerns without business logic
   - Utils: Pure functions and helpers

2. **Dependency Injection**: 
   - Pass dependencies as function parameters or constructor arguments
   - No global state or singletons (except for logger, which is a utility)

3. **Async/Await Pattern**:
   - All asynchronous operations use `async/await` syntax
   - Promises are properly handled with error catching
   - Use Node.js native async/await (no queue system needed)

### Error Handling in Code

1. **Typed Errors**: Services throw typed errors that controllers catch and handle appropriately
2. **Error Classes**: Define custom error classes in `src/errors/` for application-specific errors
3. **Error Middleware**: Errors are caught at the middleware level
4. **Error Response Format**: Error responses follow a consistent format matching Rails error responses

---

## Dependency Installation Rules

1. **Complete Installation Required**: Installing dependencies requires packages to actually be installed successfully. Updating package.json is NOT enough.

2. **No Errors Allowed**: The installation must complete successfully with no errors. If any part of the operation fails, the task is NOT complete.

3. **Check Output**: If the output mentions environmental issues, errors, warnings, or failed operations, the task is NOT complete.

4. **Verify Installation**: After installation, verify that packages are actually installed (check `node_modules/` or run package commands).

---

## Additional Rules

### Update Processing

1. **Update Types**: Handle all Telegram update types:
   - `message` (command and non-command messages)
   - `edited_message` (edited text messages)
   - `callback_query` (button callback queries)
   - Unhandled update types (should still return 200 and process)

2. **Update Filtering**: Filter out Express-specific params before processing (similar to Rails filtering `controller`, `action`, `format`, `telegram`).

3. **Chat Info Extraction**: Extract chat information from different update types (message, edited_message, callback_query) for error handling.

### Audio Processing

1. **Audio Response**: When original message was audio, should respond with audio (if audio output not disabled).

2. **Audio Transcription Error**: Should send error message to user and return early.

3. **Audio Generation Error**: Should fallback to text message if text-to-speech fails.

### Debug Mode

1. **CURSOR_DEBUG Acknowledgment**: Should send acknowledgment message when debug enabled.

2. **Debug Logging**: Use appropriate logging levels for debug information.

---

## Summary

These rules represent the consistent patterns and requirements found across all task files in the telegram-receiver application. Agents must follow these rules when:

- Implementing new features
- Converting Rails code to TypeScript/Node.js
- Writing tests
- Handling errors
- Implementing authentication
- Formatting responses
- Organizing code

When in doubt, refer to the Rails implementation in `jarek-va/` for behavior reference, and follow the patterns established in the existing TypeScript codebase.



