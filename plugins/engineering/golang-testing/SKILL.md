---
name: golang-testing
description: Expert in Go testing practices using testify framework, AAA patterns, and test utilities. **ALWAYS use when writing Go tests, creating test fixtures, or setting up test infrastructure.** Use proactively to ensure proper test structure, mock implementations, and test coverage. Examples - "write Go test", "create test fixture", "add test coverage", "mock Go interface", "setup test environment".
---

<related_skills>

→ Use `golang-engineer` skill for Go coding standards, error handling patterns, and idiomatic Go code when writing test implementations

→ Use `code-quality` skill for applying SOLID principles and Clean Code patterns when structuring test code and test helpers
</related_skills>

<critical_validations>
- Testing Framework: Use stretchr/testify for assertions and mocks, standardize all custom mocks
- Test Structure: Follow AAA pattern with blank lines separating sections, no comments
- Test Helpers: Use project test utilities for setup and fixtures
- Test File Location: Each test file must be in the same folder/directory as the file it tests
- Code Coverage: Project must maintain 95% or greater code coverage at all times
</critical_validations>

<test_file_location>

**MANDATORY:** Each test file must be in the same folder/directory as the source file it tests.

### Rules

- Test files must be placed in the same directory as the code they test
- Test file naming convention: `*_test.go` (e.g., `user_service_test.go` for `user_service.go`)
- Test files belong to the same package as the source code (can use `package_test` only for external testing)
- Never place test files in separate `test/` or `tests/` directories

### Correct Example

```
project/
├── services/
│   ├── user_service.go
│   └── user_service_test.go    ✅ Same folder
├── repositories/
│   ├── user_repository.go
│   └── user_repository_test.go  ✅ Same folder
└── models/
    ├── user.go
    └── user_test.go             ✅ Same folder
```

### Incorrect Example

```
project/
├── services/
│   └── user_service.go
├── repositories/
│   └── user_repository.go
└── tests/                        ❌ Wrong - separate test directory
    ├── user_service_test.go
    └── user_repository_test.go
```

### Benefits

- **Package Access**: Tests can access package-level (unexported) functions and types
- **Simplicity**: Easier to find and maintain tests alongside the code they test
- **Go Conventions**: Follows standard Go project structure and tooling expectations
- **IDE Support**: Better IDE support for running tests and code navigation
</test_file_location>

<testing_framework>

**MUST use:** `stretchr/testify` for assertions and mocks.

- Import as: `github.com/stretchr/testify/assert`, `github.com/stretchr/testify/mock`
- **STANDARDIZE:** Replace all custom mocks with `testify/mock` implementations
- Use `assert` package for all test assertions
- Use `mock` package for creating mock implementations of interfaces
</testing_framework>

<test_structure>

Tests must follow the Arrange-Act-Assert (AAA) pattern with blank lines separating each section. Do not use comments to mark sections.

### Rules

- **Arrange**: Set up test data, mocks, and dependencies
- **Act**: Execute the code under test
- **Assert**: Verify the expected outcomes
- Use blank lines to visually separate each section
- Never use comments like `// Arrange`, `// Act`, or `// Assert`

### Correct Example
```go
func TestUserService_CreateUser(t *testing.T) {
    ctx := context.Background()
    repo := NewMockUserRepository(t)
    service := NewUserService(repo)

    user, err := service.CreateUser(ctx, CreateUserRequest{
        Email: "test@example.com",
        Name:  "Test User",
    })

    assert.NoError(t, err)
    assert.NotNil(t, user)
    assert.Equal(t, "test@example.com", user.Email)
}
```

### Incorrect Example
```go
func TestUserService_CreateUser(t *testing.T) {
    // Arrange
    ctx := context.Background()
    repo := NewMockUserRepository(t)
    service := NewUserService(repo)
    // Act
    user, err := service.CreateUser(ctx, CreateUserRequest{
        Email: "test@example.com",
        Name:  "Test User",
    })
    // Assert
    assert.NoError(t, err)
    assert.NotNil(t, user)
    assert.Equal(t, "test@example.com", user.Email)
}
</test_structure>

<test_helpers>
Use project test utilities for consistent test setup and fixture management.

### Rules

- Use `utils.SetupTest()` to initialize test database connections
- Always defer cleanup operations (e.g., `defer db.Close()`)
- Use `utils.SetupFixture()` to load test data from JSON fixtures
- Keep test setup code consistent across all tests
- Use `t.Run()` for subtests to organize related test cases

### Example

```go
func TestService(t *testing.T) {
    t.Run("Should setup test environment", func(t *testing.T) {
        db := utils.SetupTest()
        defer db.Close()

        fixture := utils.SetupFixture("user_data.json")
        // test implementation
    })
}```
</test_helpers>

<code_coverage>

**MANDATORY:** The project must maintain **95% or greater** code coverage at all times.

### Rules

- **Minimum Coverage**: 95% code coverage is required for the entire project
- **Enforcement**: Coverage checks must be integrated into CI/CD pipeline
- **Measurement**: Use `go test -cover` and `go test -coverprofile` to measure coverage
- **Reporting**: Coverage reports should be generated and reviewed before merging code
- **New Code**: All new code must be fully tested before submission
- **Legacy Code**: Existing code below 95% coverage must be brought up to standard

### Measuring Coverage

```bash
# Run tests with coverage
go test -cover ./...

# Generate coverage profile
go test -coverprofile=coverage.out ./...

# View coverage report
go tool cover -html=coverage.out

# Check coverage percentage
go test -cover ./... | grep total | awk '{print $5}'
```
### Coverage Best Practices

1. **Test All Code Paths**: Ensure all branches, conditions, and error paths are tested
2. **Avoid Coverage Gaps**: Pay special attention to error handling and edge cases
3. **Regular Monitoring**: Check coverage regularly, not just before releases
4. **Coverage Goals**: Aim for 95%+ overall, with critical paths at 100%
5. **Exclude Generated Code**: Use build tags or coverage exclusions for generated code if needed

### Example Coverage Report

```
ok      github.com/project/services    0.123s  coverage: 96.2% of statements
ok      github.com/project/repositories 0.456s  coverage: 94.8% of statements
ok      github.com/project/models      0.234s  coverage: 97.1% of statements
total:  (statements)                   95.4%
```

**Note**: If any package falls below 95%, the build should fail. All packages must meet the minimum coverage requirement.

---

## ⚠️ CRITICAL REMINDER

**ALWAYS follow the `<critical_validations>` section at the top of this document when writing tests.**

Failure to follow these critical validations will result in non-compliant test code that must be refactored.

