# Engineering Plugin

**Expert engineering skills for backend development, code quality, Go programming, and testing**

## Overview

The Engineering Plugin provides comprehensive skills for building high-quality backend systems. These skills work together to ensure proper architecture, code quality, idiomatic Go code, and comprehensive testing.

## Skills

### `backend-engineer`

Expert backend engineer specializing in Clean Architecture, layered design, dependency management, and scalable Go backend systems.

**Use when:**
- Designing backend architecture
- Structuring Go projects
- Implementing service layers
- Establishing tech stack decisions
- Organizing codebase structure

**Key Focus Areas:**
- Clean Architecture principles
- Layer separation (domain, application, infrastructure, presentation)
- Dependency flow and inversion
- Project structure patterns
- Tech stack decisions

---

### `code-quality`

Expert in code design standards including SOLID principles, Clean Code patterns (KISS, YAGNI, DRY, TDA), and pragmatic software design.

**Use when:**
- Designing classes/modules
- Implementing features
- Fixing bugs
- Refactoring code
- Writing functions
- Ensuring maintainability

**Key Focus Areas:**
- SOLID principles
- Clean Code patterns (KISS, YAGNI, DRY, TDA)
- Small functions and meaningful names
- Single level of abstraction
- Early returns and guard clauses
- Anti-pattern avoidance

---

### `golang-engineer`

Expert Go engineer specializing in idiomatic Go code, Clean Architecture, SOLID principles, and enterprise-grade patterns.

**Use when:**
- Writing Go code
- Designing Go modules
- Implementing Go features
- Refactoring Go codebases
- Handling Go errors
- Using Go concurrency

**Key Focus Areas:**
- Idiomatic Go patterns
- Error handling strategies
- Concurrency patterns
- Interface design
- Context usage
- Map operations (using core utilities)
- Logging patterns

---

### `golang-testing`

Expert in Go testing practices using testify framework, AAA patterns, and test utilities.

**Use when:**
- Writing Go tests
- Creating test fixtures
- Setting up test infrastructure
- Adding test coverage
- Mocking Go interfaces

**Key Focus Areas:**
- Testify framework
- AAA (Arrange-Act-Assert) patterns
- Test structure and organization
- Test file location (same directory as source)
- Code coverage (95%+ requirement)
- Mock implementations

---

## When to Use SKILL

Skills in this plugin are designed to be invoked directly in conversation to provide comprehensive guidance, best practices, and detailed context. Understanding when and how to use them optimizes your development workflow.

### Direct Skill Invocation

**Use Skills when:**
- You need comprehensive guidance and detailed context
- You want to understand best practices and patterns
- You're learning or need examples
- You need flexible, context-aware assistance
- You want expert-level guidance on specific topics
- You're making architectural or design decisions

**How to Invoke:**
```bash
# Direct skill invocation with context
@backend-engineer "design the architecture for a user management service"

@code-quality "review this function for SOLID principles"

@golang-engineer "implement error handling for this API endpoint"

@golang-testing "write tests for this service with 95% coverage"
```

### Characteristics of Skills

- **Comprehensive Documentation**: Extensive examples, patterns, and best practices
- **Context-Aware**: Provide guidance based on your specific situation
- **Educational**: Help you understand why certain patterns are recommended
- **Flexible**: Can be invoked with specific questions or scenarios
- **Expert-Level**: Deep knowledge of industry standards and best practices

### Decision Matrix

| Scenario | Use Skill | Why |
|---------|-----------|-----|
| Designing backend architecture | `@backend-engineer` | Clean Architecture guidance |
| Structuring a new Go project | `@backend-engineer` | Project structure patterns |
| Reviewing code for quality | `@code-quality` | SOLID and Clean Code principles |
| Refactoring existing code | `@code-quality` | Design patterns and anti-patterns |
| Writing Go functions | `@golang-engineer` | Idiomatic Go patterns |
| Handling errors in Go | `@golang-engineer` | Error handling strategies |
| Writing tests | `@golang-testing` | Testify patterns and AAA structure |
| Setting up test infrastructure | `@golang-testing` | Test utilities and fixtures |
| Making tech stack decisions | `@backend-engineer` | Recommended libraries and frameworks |

### Best Practices

1. **Invoke Early**: Use skills at the beginning of a task to get guidance
2. **Be Specific**: Provide context about what you're working on
3. **Combine Skills**: Use multiple skills together for comprehensive coverage
4. **Follow Patterns**: Skills provide proven patterns - trust them
5. **Learn from Examples**: Skills include extensive examples - study them

---

## Skill Relationships

Skills in this plugin are designed to work together, with clear relationships and dependencies. Understanding these relationships helps you use them effectively and ensures comprehensive coverage of all aspects of development.

### Core Relationship Graph

```
backend-engineer (Architecture & Structure)
    ├──→ golang-engineer (Implementation)
    │       ├──→ code-quality (Design Principles)
    │       └──→ golang-testing (Testing)
    │
    ├──→ code-quality (Design Standards)
    │       ├──→ golang-engineer (Go-specific Application)
    │       └──→ golang-testing (Test Code Quality)
    │
    └──→ golang-testing (Testing)
            ├──→ golang-engineer (Test Implementation)
            └──→ code-quality (Test Structure)
```

### Detailed Relationships

#### `backend-engineer` → `golang-engineer`

**Relationship**: Architectural → Implementation

The `backend-engineer` skill provides architectural guidance and project structure, while `golang-engineer` handles the implementation details.

```markdown
## Related Skills

→ Use `golang-engineer` skill for Go-specific implementation patterns, 
  error handling, concurrency, and idiomatic Go code when implementing 
  backend services
```

**Usage Pattern:**
1. Use `@backend-engineer` to design architecture and structure
2. Use `@golang-engineer` to implement the designed components

**Example:**
```bash
# Step 1: Design architecture
@backend-engineer "design the service layer for user management"

# Step 2: Implement with Go patterns
@golang-engineer "implement the user service with proper error handling"
```

---

#### `backend-engineer` → `code-quality`

**Relationship**: Architecture → Design Standards

The `backend-engineer` skill focuses on overall architecture, while `code-quality` ensures design principles are applied.

```markdown
## Related Skills

→ Use `code-quality` skill for SOLID principles, Clean Code patterns, 
  and design standards when designing backend modules and services
```

**Usage Pattern:**
- Use `@backend-engineer` for architectural decisions
- Use `@code-quality` to ensure modules follow design principles

---

#### `backend-engineer` → `golang-testing`

**Relationship**: Architecture → Testing Strategy

The `backend-engineer` skill designs the structure, while `golang-testing` ensures comprehensive test coverage.

```markdown
## Related Skills

→ Use `golang-testing` skill for writing comprehensive tests and 
  maintaining high code coverage for backend services
```

**Usage Pattern:**
- Design architecture with `@backend-engineer`
- Write tests with `@golang-testing` to ensure coverage

---

#### `code-quality` → `golang-engineer`

**Relationship**: Design Principles → Go Implementation

The `code-quality` skill provides general design principles, while `golang-engineer` applies them specifically to Go code.

```markdown
## Related Skills

→ Use `golang-engineer` skill for Go-specific implementation patterns, 
  error handling, and idiomatic Go code when applying these principles 
  to Go projects
```

**Usage Pattern:**
- Apply SOLID principles with `@code-quality`
- Implement in Go idiomatically with `@golang-engineer`

---

#### `code-quality` → `golang-testing`

**Relationship**: Design Standards → Test Quality

The `code-quality` skill ensures test code also follows design principles.

```markdown
## Related Skills

→ Use `golang-testing` skill for writing tests that follow these code 
  quality principles and ensuring proper test coverage
```

**Usage Pattern:**
- Structure test code with `@code-quality` principles
- Use `@golang-testing` for test-specific patterns

---

#### `golang-engineer` → `code-quality`

**Relationship**: Implementation → Design Review

The `golang-engineer` skill implements code, while `code-quality` ensures it follows design principles.

```markdown
## Related Skills

→ Use `code-quality` skill for comprehensive SOLID principles, Clean 
  Code patterns (KISS, YAGNI, DRY, TDA), and design standards when 
  designing Go modules and implementing features
```

**Usage Pattern:**
- Write Go code with `@golang-engineer`
- Review with `@code-quality` to ensure design quality

---

#### `golang-engineer` → `golang-testing`

**Relationship**: Implementation → Testing

The `golang-engineer` skill writes the code, while `golang-testing` ensures it's properly tested.

```markdown
## Related Skills

→ Use `golang-testing` skill for writing tests following testify 
  framework, AAA patterns, and maintaining 95%+ code coverage
```

**Usage Pattern:**
- Implement features with `@golang-engineer`
- Write tests with `@golang-testing`

---

#### `golang-testing` → `golang-engineer`

**Relationship**: Testing → Test Implementation

The `golang-testing` skill provides test patterns, while `golang-engineer` ensures tests follow Go idioms.

```markdown
## Related Skills

→ Use `golang-engineer` skill for Go coding standards, error handling 
  patterns, and idiomatic Go code when writing test implementations
```

**Usage Pattern:**
- Structure tests with `@golang-testing`
- Implement test code idiomatically with `@golang-engineer`

---

#### `golang-testing` → `code-quality`

**Relationship**: Testing → Test Design

The `golang-testing` skill focuses on test patterns, while `code-quality` ensures test code quality.

```markdown
## Related Skills

→ Use `code-quality` skill for applying SOLID principles and Clean 
  Code patterns when structuring test code and test helpers
```

**Usage Pattern:**
- Write tests with `@golang-testing`
- Ensure test structure follows `@code-quality` principles

---

### Skill Relationship Types

1. **Sequential** (`backend-engineer` → `golang-engineer`)
   - One skill naturally follows another in the workflow
   - Architecture design → Implementation

2. **Complementary** (`code-quality` ↔ `golang-engineer`)
   - Skills work together but aren't strictly sequential
   - Design principles applied to Go implementation

3. **Prerequisite** (`backend-engineer` → `golang-testing`)
   - One skill provides foundation for another
   - Architecture must be designed before comprehensive testing

4. **Informational** (`backend-engineer` → `golang-engineer`)
   - Skills provide context about related capabilities
   - Helps understand when to use related skills

### Complete Workflow Example

Here's how skills work together in a typical development workflow:

```bash
# 1. Design Architecture
@backend-engineer "design service layer for user management with Clean Architecture"

# 2. Apply Design Principles
@code-quality "ensure the service design follows SOLID principles"

# 3. Implement in Go
@golang-engineer "implement the user service with proper error handling and logging"

# 4. Review Code Quality
@code-quality "review the implementation for Clean Code patterns"

# 5. Write Tests
@golang-testing "write comprehensive tests for the user service with 95% coverage"

# 6. Ensure Test Quality
@code-quality "ensure test code follows design principles"
```

### Best Practices for Skill Relationships

1. **Start with Architecture**: Begin with `@backend-engineer` for structure
2. **Apply Design Principles**: Use `@code-quality` throughout
3. **Implement Idiomatically**: Use `@golang-engineer` for Go-specific patterns
4. **Test Comprehensively**: Use `@golang-testing` for all implementations
5. **Follow the Chain**: When a skill references another, consider using it
6. **Read Related Skills**: Check the "Related Skills" section in each skill
7. **Combine Skills**: Use multiple skills together for complete coverage

---

## Finding Additional Documentation

When skills don't provide enough detail or you need up-to-date documentation for specific libraries, frameworks, or tools, use **Perplexity MCP** or **Context7** to search for additional documentation.

### When to Use Documentation Search

**Use Perplexity MCP or Context7 when:**
- You need specific library/framework documentation
- You want the latest API documentation
- You need examples for a particular library version
- Skills don't cover a specific library you're using
- You want to verify current best practices for a tool
- You need troubleshooting information for a specific library

### Perplexity MCP

**Use Perplexity MCP for:**
- General web searches for documentation
- Finding examples and tutorials
- Searching for troubleshooting guides
- Getting up-to-date information from multiple sources
- Finding community discussions and solutions

**Example Usage:**
```bash
# Search for library documentation
@perplexity "gin-gonic/gin middleware documentation"

# Find examples
@perplexity "go-playground/validator custom validation examples"

# Troubleshooting
@perplexity "testify mock expectations not working"
```

### Context7

**Use Context7 for:**
- Official library documentation
- API reference documentation
- Library-specific patterns and best practices
- Version-specific documentation
- Structured, authoritative documentation

**Example Usage:**
```bash
# Get library documentation
@context7 "gin-gonic/gin"

# Get specific version docs
@context7 "golang-migrate/migrate v4"

# Focus on specific topics
@context7 "stretchr/testify" topic="mocking"
```

### Integration with Skills

**Workflow Pattern:**
1. Start with skills for general guidance
2. Use Perplexity MCP or Context7 for specific library details
3. Return to skills to apply the documentation in context

**Example:**
```bash
# Step 1: Get architectural guidance
@backend-engineer "design API with gin framework"

# Step 2: Get specific gin documentation
@context7 "gin-gonic/gin" topic="middleware"

# Step 3: Apply with Go patterns
@golang-engineer "implement gin middleware with proper error handling"
```

### Best Practices

1. **Start with Skills**: Use skills first for general patterns and principles
2. **Search for Specifics**: Use Perplexity MCP or Context7 for library-specific details
3. **Verify Versions**: Check documentation matches your library version
4. **Combine Sources**: Use both tools - Context7 for official docs, Perplexity for examples
5. **Apply with Skills**: Always apply documentation findings using relevant skills

### Common Documentation Needs

| Need | Tool | Example |
|------|------|---------|
| Official API docs | Context7 | `@context7 "gin-gonic/gin"` |
| Examples & tutorials | Perplexity MCP | `@perplexity "gin middleware examples"` |
| Version-specific docs | Context7 | `@context7 "golang-migrate/migrate v4"` |
| Troubleshooting | Perplexity MCP | `@perplexity "testify mock not working"` |
| Best practices | Both | Start with Context7, supplement with Perplexity |

---

## File Structure

```
engineering/
├── backend/
│   └── SKILL.md          # Backend architecture and structure
├── code-quality/
│   └── SKILL.md          # SOLID principles and Clean Code
├── golang-engineer/
│   └── SKILL.md          # Go-specific patterns and idioms
├── golang-testing/
│   └── SKILL.md          # Go testing with testify
└── README.md             # This file
```

---

## Quick Reference

### By Task Type

| Task | Primary Skill | Supporting Skills |
|------|--------------|------------------|
| Design architecture | `@backend-engineer` | `@code-quality` |
| Structure project | `@backend-engineer` | `@golang-engineer` |
| Write Go code | `@golang-engineer` | `@code-quality` |
| Refactor code | `@code-quality` | `@golang-engineer` |
| Write tests | `@golang-testing` | `@golang-engineer`, `@code-quality` |
| Review code | `@code-quality` | `@golang-engineer` |
| Handle errors | `@golang-engineer` | `@code-quality` |
| Use concurrency | `@golang-engineer` | `@code-quality` |

### By Concern

| Concern | Skills to Use |
|---------|---------------|
| Architecture | `@backend-engineer` |
| Design principles | `@code-quality` |
| Go idioms | `@golang-engineer` |
| Testing | `@golang-testing` |
| Error handling | `@golang-engineer` |
| Code organization | `@backend-engineer`, `@code-quality` |

---

## Tips

- **Invoke skills early**: Get guidance at the start of tasks
- **Combine skills**: Use multiple skills for comprehensive coverage
- **Follow relationships**: When a skill references another, use it
- **Trust the patterns**: Skills provide proven, industry-standard patterns
- **Learn from examples**: Skills include extensive examples - study them
- **Maintain consistency**: Use skills consistently across the project
- **Search for documentation**: Use Perplexity MCP or Context7 when you need specific library documentation or examples
