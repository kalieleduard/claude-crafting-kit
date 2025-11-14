# Engineering Plugin

**Expert engineering skills for backend development, code quality, Go programming, TypeScript/JavaScript development, Bun.js runtime, and testing**

## Overview

The Engineering Plugin provides comprehensive skills for building high-quality backend systems. These skills work together to ensure proper architecture, code quality, idiomatic Go code, TypeScript type safety, Bun.js optimization, and comprehensive testing.

## Skills

### `backend`

Expert backend specializing in Clean Architecture, layered design, dependency management, and scalable Go backend systems.

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

### `code-standards`

Expert in code formatting and style standards including function length limits, parameter count limits, no line breaks in functions, no comments (use clarifying names), and no magic numbers.

**Use when:**
- Formatting code
- Reviewing code style
- Ensuring consistent formatting
- Refactoring for readability
- Enforcing code standards
- Improving code consistency

**Key Focus Areas:**
- No line breaks inside functions
- No comments (use clarifying names instead)
- Function length limits (maximum 30 lines)
- Function parameter limits (few parameters, ideally 3 or fewer)
- No magic numbers (use named constants)

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

### `bun`

Expert in Bun.js runtime, bundler, test runner, and package manager. Specializes in high-performance JavaScript/TypeScript applications, native APIs, SQLite integration, HTTP servers, and Bun-specific optimizations.

**Use when:**
- Building Bun.js applications
- Optimizing Bun performance
- Working with native Bun APIs
- Leveraging Bun's built-in features
- Setting up Bun projects
- Writing Bun tests
- Bundling with Bun

**Key Focus Areas:**
- Native Bun APIs (bun:sqlite, Bun.serve, Bun.file)
- SQLite integration and transactions
- HTTP server with Bun.serve()
- File operations and streaming
- Performance optimization
- TypeScript configuration for Bun
- Package management with Bun

---

### `typescript`

Expert in TypeScript type system, strict mode, generics, utility types, and advanced type patterns. Specializes in type safety, inference, and leveraging TypeScript's powerful type system for robust applications.

**Use when:**
- Writing TypeScript code
- Designing type-safe APIs
- Working with generics
- Configuring TypeScript projects
- Improving type safety
- Using utility types
- Implementing advanced type patterns

**Key Focus Areas:**
- Strict mode configuration
- Type safety and inference
- Generics and constraints
- Utility types (Pick, Omit, Partial, Record, etc.)
- Advanced types (conditional, mapped, template literals)
- Interfaces vs type aliases
- Null safety and type guards

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
@backend "design the architecture for a user management service"

@code-quality "review this function for SOLID principles"

@code-standards "format this function according to code standards"

@golang-engineer "implement error handling for this API endpoint"

@golang-testing "write tests for this service with 95% coverage"

@bun "create a REST API server with SQLite database"

@typescript "design type-safe API with generics and utility types"
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
| Designing backend architecture | `@backend` | Clean Architecture guidance |
| Structuring a new Go project | `@backend` | Project structure patterns |
| Reviewing code for quality | `@code-quality` | SOLID and Clean Code principles |
| Refactoring existing code | `@code-quality` | Design patterns and anti-patterns |
| Formatting code | `@code-standards` | Code formatting and style standards |
| Ensuring code consistency | `@code-standards` | Consistent formatting rules |
| Writing Go functions | `@golang-engineer` | Idiomatic Go patterns |
| Handling errors in Go | `@golang-engineer` | Error handling strategies |
| Writing tests | `@golang-testing` | Testify patterns and AAA structure |
| Setting up test infrastructure | `@golang-testing` | Test utilities and fixtures |
| Making tech stack decisions | `@backend` | Recommended libraries and frameworks |
| Building Bun.js applications | `@bun` | Bun runtime, APIs, and best practices |
| Optimizing Bun performance | `@bun` | Native APIs and performance patterns |
| Using Bun SQLite | `@bun` | Database operations and transactions |
| Writing TypeScript code | `@typescript` | Type safety and best practices |
| Configuring TypeScript | `@typescript` | Strict mode and compiler options |
| Designing type-safe APIs | `@typescript` | Generics and utility types |

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
backend (Architecture & Structure)
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

#### `backend` → `golang-engineer`

**Relationship**: Architectural → Implementation

The `backend` skill provides architectural guidance and project structure, while `golang-engineer` handles the implementation details.

```markdown
## Related Skills

→ Use `golang-engineer` skill for Go-specific implementation patterns, 
  error handling, concurrency, and idiomatic Go code when implementing 
  backend services
```

**Usage Pattern:**
1. Use `@backend` to design architecture and structure
2. Use `@golang-engineer` to implement the designed components

**Example:**
```bash
# Step 1: Design architecture
@backend "design the service layer for user management"

# Step 2: Implement with Go patterns
@golang-engineer "implement the user service with proper error handling"
```

---

#### `backend` → `code-quality`

**Relationship**: Architecture → Design Standards

The `backend` skill focuses on overall architecture, while `code-quality` ensures design principles are applied.

```markdown
## Related Skills

→ Use `code-quality` skill for SOLID principles, Clean Code patterns, 
  and design standards when designing backend modules and services
```

**Usage Pattern:**
- Use `@backend` for architectural decisions
- Use `@code-quality` to ensure modules follow design principles

---

#### `backend` → `golang-testing`

**Relationship**: Architecture → Testing Strategy

The `backend` skill designs the structure, while `golang-testing` ensures comprehensive test coverage.

```markdown
## Related Skills

→ Use `golang-testing` skill for writing comprehensive tests and 
  maintaining high code coverage for backend services
```

**Usage Pattern:**
- Design architecture with `@backend`
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

#### `bun` → `typescript`

**Relationship**: Runtime → Type System

The `bun` skill provides Bun.js runtime guidance, while `typescript` ensures type safety for Bun applications.

```markdown
## Related Skills

→ Use `typescript` skill for TypeScript type system, strict mode, 
  generics, and utility types when building Bun.js applications
```

**Usage Pattern:**
- Build Bun.js applications with `@bun`
- Ensure type safety with `@typescript`

---

#### `bun` → `backend`

**Relationship**: Runtime → Architecture

The `bun` skill handles Bun.js-specific implementation, while `backend` provides architectural guidance.

```markdown
## Related Skills

→ Use `backend` skill for Clean Architecture, layered design, and 
  project structure when building backend services with Bun
```

**Usage Pattern:**
- Design architecture with `@backend`
- Implement with Bun.js using `@bun`

---

#### `bun` → `code-quality`

**Relationship**: Implementation → Design Standards

The `bun` skill implements Bun.js features, while `code-quality` ensures design principles are followed.

```markdown
## Related Skills

→ Use `code-quality` skill for SOLID principles, Clean Code patterns, 
  and design standards when designing Bun applications
```

**Usage Pattern:**
- Build Bun.js features with `@bun`
- Review with `@code-quality` to ensure design quality

---

#### `typescript` → `code-quality`

**Relationship**: Type System → Design Standards

The `typescript` skill ensures type safety, while `code-quality` ensures design principles are applied.

```markdown
## Related Skills

→ Use `code-quality` skill for SOLID principles and Clean Code patterns 
  when designing TypeScript applications
```

**Usage Pattern:**
- Write type-safe code with `@typescript`
- Review with `@code-quality` to ensure design quality

---

#### `typescript` → `code-standards`

**Relationship**: Type System → Code Formatting

The `typescript` skill handles type safety, while `code-standards` ensures consistent formatting.

```markdown
## Related Skills

→ Use `code-standards` skill for code formatting and style standards 
  when writing TypeScript code
```

**Usage Pattern:**
- Write TypeScript code with `@typescript`
- Format with `@code-standards` for consistency

---

#### `typescript` → `backend`

**Relationship**: Type System → Architecture

The `typescript` skill ensures type safety, while `backend` provides architectural patterns.

```markdown
## Related Skills

→ Use `backend` skill for Clean Architecture and project structure 
  when building TypeScript backend services
```

**Usage Pattern:**
- Design architecture with `@backend`
- Implement with type safety using `@typescript`

---

### Skill Relationship Types

1. **Sequential** (`backend` → `golang-engineer`)
   - One skill naturally follows another in the workflow
   - Architecture design → Implementation

2. **Complementary** (`code-quality` ↔ `golang-engineer`)
   - Skills work together but aren't strictly sequential
   - Design principles applied to Go implementation

3. **Prerequisite** (`backend` → `golang-testing`)
   - One skill provides foundation for another
   - Architecture must be designed before comprehensive testing

4. **Informational** (`backend` → `golang-engineer`)
   - Skills provide context about related capabilities
   - Helps understand when to use related skills

### Complete Workflow Example

Here's how skills work together in a typical development workflow:

#### Go Backend Workflow

```bash
# 1. Design Architecture
@backend "design service layer for user management with Clean Architecture"

# 2. Apply Design Principles
@code-quality "ensure the service design follows SOLID principles"

# 3. Implement in Go
@golang-engineer "implement the user service with proper error handling and logging"

# 4. Review Code Quality
@code-quality "review the implementation for Clean Code patterns"

# 5. Apply Code Standards
@code-standards "format the code according to style standards"

# 6. Write Tests
@golang-testing "write comprehensive tests for the user service with 95% coverage"

# 7. Ensure Test Quality
@code-quality "ensure test code follows design principles"
```

#### Bun.js/TypeScript Workflow

```bash
# 1. Design Architecture
@backend "design REST API architecture for user management with Clean Architecture"

# 2. Configure TypeScript
@typescript "configure TypeScript with strict mode for the project"

# 3. Design Type-Safe APIs
@typescript "design type-safe API interfaces with generics and utility types"

# 4. Apply Design Principles
@code-quality "ensure the API design follows SOLID principles"

# 5. Implement with Bun.js
@bun "implement REST API server with Bun.serve() and SQLite database"

# 6. Review Code Quality
@code-quality "review the implementation for Clean Code patterns"

# 7. Apply Code Standards
@code-standards "format the TypeScript code according to style standards"

# 8. Write Tests
@bun "write comprehensive tests for the API with Bun's test runner"

# 9. Ensure Type Safety
@typescript "review type safety and ensure proper use of utility types"
```

### Best Practices for Skill Relationships

1. **Start with Architecture**: Begin with `@backend` for structure
2. **Apply Design Principles**: Use `@code-quality` throughout
3. **Enforce Code Standards**: Use `@code-standards` for formatting and style
4. **Implement Idiomatically**: Use `@golang-engineer` for Go-specific patterns
5. **Test Comprehensively**: Use `@golang-testing` for all implementations
6. **Follow the Chain**: When a skill references another, consider using it
7. **Read Related Skills**: Check the "Related Skills" section in each skill
8. **Combine Skills**: Use multiple skills together for complete coverage

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
@backend "design API with gin framework"

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
├── bun/
│   └── SKILL.md          # Bun.js runtime, APIs, and best practices
├── code-quality/
│   └── SKILL.md          # SOLID principles and Clean Code
├── code-standards/
│   └── SKILL.md          # Code formatting and style standards
├── golang-engineer/
│   └── SKILL.md          # Go-specific patterns and idioms
├── golang-testing/
│   └── SKILL.md          # Go testing with testify
├── typescript/
│   └── SKILL.md          # TypeScript type system and best practices
└── README.md             # This file
```

---

## Quick Reference

### By Task Type

| Task | Primary Skill | Supporting Skills |
|------|--------------|------------------|
| Design architecture | `@backend` | `@code-quality` |
| Structure project | `@backend` | `@golang-engineer` |
| Write Go code | `@golang-engineer` | `@code-quality`, `@code-standards` |
| Refactor code | `@code-quality` | `@golang-engineer`, `@code-standards` |
| Format code | `@code-standards` | `@code-quality` |
| Write tests | `@golang-testing` | `@golang-engineer`, `@code-quality` |
| Review code | `@code-quality` | `@golang-engineer`, `@code-standards` |
| Handle errors | `@golang-engineer` | `@code-quality` |
| Use concurrency | `@golang-engineer` | `@code-quality` |
| Build Bun.js apps | `@bun` | `@typescript`, `@code-quality`, `@code-standards` |
| Write TypeScript | `@typescript` | `@code-quality`, `@code-standards` |
| Configure TypeScript | `@typescript` | `@bun` (if using Bun) |
| Design type-safe APIs | `@typescript` | `@code-quality`, `@backend` |

### By Concern

| Concern | Skills to Use |
|---------|---------------|
| Architecture | `@backend` |
| Design principles | `@code-quality` |
| Code formatting | `@code-standards` |
| Go idioms | `@golang-engineer` |
| Testing | `@golang-testing` |
| Error handling | `@golang-engineer` |
| Code organization | `@backend`, `@code-quality` |
| Bun.js runtime | `@bun` |
| TypeScript types | `@typescript` |
| Type safety | `@typescript` |
| Bun APIs | `@bun` |

---

## Tips

- **Invoke skills early**: Get guidance at the start of tasks
- **Combine skills**: Use multiple skills for comprehensive coverage
- **Follow relationships**: When a skill references another, use it
- **Trust the patterns**: Skills provide proven, industry-standard patterns
- **Learn from examples**: Skills include extensive examples - study them
- **Maintain consistency**: Use skills consistently across the project
- **Search for documentation**: Use Perplexity MCP or Context7 when you need specific library documentation or examples
