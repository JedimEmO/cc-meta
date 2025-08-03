---
name: review-branch
description: Perform a thorough security-focused code review of the current branch against main/master
version: 1.0.0
author: Security Review Bot
tags: [git, review, security, testing, code-quality]
---

# Branch Review Command

Performs a comprehensive security-focused code review of the current branch compared to the main branch (master/main).

## Usage

```bash
/review-branch [base-branch]
```

## Parameters

- `base-branch` (optional): The base branch to compare against (defaults to 'main' or 'master')

## What This Command Does

1. **Identifies the base branch** - Automatically detects main/master or uses specified branch
2. **Analyzes the full diff** - Reviews ALL changes that would be merged
3. **Security audit** - Identifies potential vulnerabilities
4. **Dead code detection** - Finds unused code, imports, and functions
5. **Test coverage** - Evaluates testing adequacy and testability
6. **Code quality** - Checks for best practices and maintainability

## Review Process

### 1. Pre-Review Setup
```bash
# Ensure we have the latest base branch
git fetch origin

# Identify all files changed in this branch
git diff --name-only origin/{base-branch}...HEAD

# Get the full diff for review
git diff origin/{base-branch}...HEAD
```

### 2. Security Review Checklist

The command will check for:

- **Authentication & Authorization Issues**
  - Hardcoded credentials, API keys, or secrets
  - Missing authentication checks
  - Improper access control
  - JWT/token handling vulnerabilities

- **Input Validation & Sanitization**
  - SQL injection vulnerabilities
  - XSS (Cross-Site Scripting) risks
  - Command injection possibilities
  - Path traversal vulnerabilities
  - Unvalidated user input

- **Data Security**
  - Sensitive data exposure in logs
  - Unencrypted sensitive data storage
  - Insecure data transmission
  - PII (Personally Identifiable Information) handling

- **Dependencies & Libraries**
  - New dependencies with known vulnerabilities
  - Outdated security-critical packages
  - Unnecessary permission requirements

- **Configuration Security**
  - Insecure default configurations
  - Debug mode left enabled
  - Verbose error messages exposing internals

### 3. Dead Code Analysis

The review will identify:

- **Unused Imports**: Modules imported but never used
- **Unreachable Code**: Code after return/throw statements
- **Unused Functions/Methods**: Defined but never called
- **Unused Variables**: Declared but never referenced
- **Commented-Out Code**: Old code that should be removed
- **Unused Type Definitions**: TypeScript/Flow types that aren't used
- **Orphaned Files**: Files not imported anywhere

### 4. Testing & Testability Review

#### Test Coverage Analysis
- Identifies untested new code
- Checks if critical paths have tests
- Reviews test quality (not just presence)

#### Testability Concerns
- **Tight Coupling**: Code that's hard to test in isolation
- **Hidden Dependencies**: Direct file system/network calls
- **Large Functions**: Functions doing too much (>50 lines)
- **Complex Logic**: High cyclomatic complexity
- **Missing Abstractions**: Hardcoded values that should be configurable

#### Testing Best Practices
- Tests should be deterministic
- Each test should test one thing
- Test names should be descriptive
- No test interdependencies
- Proper setup/teardown

### 5. Code Quality Metrics

- **Function Complexity**: Cyclomatic complexity > 10
- **File Length**: Files > 300 lines
- **Function Length**: Functions > 50 lines
- **Nesting Depth**: More than 4 levels of nesting
- **Duplicate Code**: Similar code patterns that should be refactored

## Output Format

The command provides a structured report:

```
🔍 BRANCH REVIEW REPORT
=======================
Branch: feature/new-api -> main
Files Changed: 15
Lines Added: +500 / Removed: -200

🚨 SECURITY ISSUES (3)
----------------------
1. [HIGH] Potential SQL Injection - src/db/queries.js:45
   - User input directly concatenated in query
   - Recommendation: Use parameterized queries

2. [MEDIUM] Hardcoded API Key - src/config/api.js:12
   - API key found in source code
   - Recommendation: Use environment variables

3. [LOW] Verbose Error Messages - src/handlers/error.js:23
   - Stack traces exposed to users
   - Recommendation: Log detailed errors, return generic messages

💀 DEAD CODE DETECTED (5)
-------------------------
1. Unused Import - src/utils/helpers.js:3
   - 'lodash' imported but never used

2. Unreachable Code - src/api/user.js:67-70
   - Code after return statement

3. Unused Function - src/lib/legacy.js:45
   - Function 'oldProcessor' defined but never called

🧪 TESTING CONCERNS (4)
-----------------------
1. Untested Code - src/api/newEndpoint.js
   - No tests found for new endpoint
   - Critical path requires testing

2. Low Test Coverage - src/services/payment.js
   - Only 30% coverage on payment logic
   - High-risk code needs better coverage

3. Testability Issue - src/workers/emailer.js:23
   - Direct SMTP connection in function
   - Recommendation: Inject email service dependency

4. Complex Untested Logic - src/calculators/tax.js:45-120
   - Complex tax calculation without tests
   - Cyclomatic complexity: 15

📊 CODE QUALITY METRICS
-----------------------
- Average Function Length: 35 lines (✓ Good)
- Max Function Length: 127 lines (⚠️ Refactor recommended)
- Average Complexity: 8 (✓ Acceptable)
- Max Complexity: 18 (⚠️ High - consider splitting)
- Duplicate Code: 2 similar patterns found

📋 RECOMMENDATIONS
------------------
1. Address all HIGH security issues before merging
2. Add tests for all new endpoints and critical paths
3. Remove identified dead code
4. Refactor complex functions for better testability
5. Configure security linting in CI/CD pipeline

✅ POSITIVE FINDINGS
--------------------
- Good error handling patterns in most files
- Consistent code style
- Proper async/await usage
- Good separation of concerns in new modules
```

## Instructions for Manual Review

After running this command, manually verify:

1. **Business Logic**: Ensure the implementation matches requirements
2. **Edge Cases**: Check handling of null, undefined, empty values
3. **Performance**: Look for N+1 queries, unnecessary loops
4. **Documentation**: Ensure new code is properly documented
5. **Breaking Changes**: Verify backward compatibility

## Integration Tips

1. Run this before creating a pull request
2. Address all HIGH severity issues
3. Aim for >80% test coverage on new code
4. Remove all dead code before merging
5. Consider running periodically on long-lived branches

## Example Workflow

```bash
# 1. Ensure your branch is up to date
git fetch origin
git rebase origin/main

# 2. Run the review
/review-branch

# 3. Address issues found
# Fix security vulnerabilities
# Add missing tests
# Remove dead code

# 4. Run review again to verify fixes
/review-branch

# 5. Create pull request when clean
```

## Configuration

You can customize the review by creating `.review-config.json`:

```json
{
  "security": {
    "checkDependencies": true,
    "scanLevel": "strict"
  },
  "testing": {
    "minCoverage": 80,
    "requireTestsForNewFiles": true
  },
  "complexity": {
    "maxFunctionLength": 50,
    "maxCyclomaticComplexity": 10,
    "maxFileLength": 300
  },
  "deadCode": {
    "reportCommentedCode": true,
    "reportUnusedExports": true
  }
}
```

## Note on AI Limitations

This command uses AI analysis which may:
- Generate false positives (review findings carefully)
- Miss some subtle vulnerabilities
- Not understand all business logic contexts

Always combine with human review and other security tools.