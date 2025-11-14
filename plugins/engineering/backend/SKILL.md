---
name: backend-engineer
description: Expert backend engineer specializing in Clean Architecture, layered design, dependency management, and scalable Go backend systems. **ALWAYS use when designing backend architecture, structuring Go projects, implementing services, or establishing tech stack decisions.** Use proactively to ensure proper separation of concerns, dependency flow, and adherence to architectural best practices. Examples - "design backend architecture", "structure Go project", "implement service layer", "setup tech stack", "organize codebase", "design API", "create microservice".
---

<critical_validations>
- Architecture: Follow Clean Architecture principles with clear layer separation (domain, application, infrastructure, presentation); enforce dependency flow from outer layers to inner layers; never allow inner layers to depend on outer layers.
- Type Declaration Flow: Declare interfaces in the file where they are consumed (service/consumer), not where they are implemented (repository/provider); interfaces belong to the consumer, not the provider.
- Project Structure: Organize code by feature/domain with consistent directory patterns; separate interfaces from implementations; group related code together; maintain clear boundaries between layers.
- Tech Stack: Use established Go libraries and frameworks; gin-gonic/gin for HTTP APIs, go-playground/validator for validation, spf13/cobra for CLI, swaggo/swag for documentation; prefer standard library and proven patterns over custom solutions.
</critical_validations>

<related_skills>

→ Use `golang-engineer` skill for Go-specific implementation patterns, error handling, concurrency, and idiomatic Go code when implementing backend services

→ Use `code-quality` skill for SOLID principles, Clean Code patterns, and design standards when designing backend modules and services

→ Use `golang-testing` skill for writing comprehensive tests and maintaining high code coverage for backend services

→ **Informational:** `backend-engineer` skill provides architectural guidance and project structure patterns; refer to `golang-engineer` for detailed implementation patterns and Go-specific best practices
</related_skills>

<architecture>

### Clean Architecture Principles

Clean Architecture organizes code into concentric layers with clear dependency rules. The core principle is that dependencies point inward: outer layers depend on inner layers, but inner layers never depend on outer layers.

#### Layer Structure

```
┌─────────────────────────────────────┐
│     Presentation Layer (HTTP/CLI)   │  ← Outermost
├─────────────────────────────────────┤
│     Application Layer (Use Cases)   │
├─────────────────────────────────────┤
│     Domain Layer (Entities/Business)│  ← Innermost
├─────────────────────────────────────┤
│  Infrastructure Layer (DB/External) │  ← Outermost
└─────────────────────────────────────┘
```

#### Dependency Flow Rules

1. **Dependency Direction:** Dependencies flow inward only
   - Presentation → Application → Domain
   - Infrastructure → Application → Domain
   - Domain has no dependencies (pure business logic)

2. **Interface Definition:** Inner layers define interfaces; outer layers implement them
   - Domain defines what it needs (interfaces)
   - Infrastructure implements those interfaces

3. **No Circular Dependencies:** Layers cannot depend on each other horizontally

#### Layer Responsibilities

**Domain Layer (Innermost)**
- Contains business entities and core business logic
- Defines interfaces for what it needs (repositories, external services)
- No dependencies on other layers
- Pure Go code with no external frameworks

```go
// Domain: User entity
package domain

type User struct {
    ID    string
    Email string
    Name  string
}

func (u *User) Validate() error {
    if u.Email == "" {
        return fmt.Errorf("email is required")
    }
    if !strings.Contains(u.Email, "@") {
        return fmt.Errorf("invalid email format")
    }
    return nil
}

// Domain: Repository interface (what domain needs)
type UserRepository interface {
    Save(ctx context.Context, user *User) error
    FindByID(ctx context.Context, id string) (*User, error)
    FindByEmail(ctx context.Context, email string) (*User, error)
}
```

**Application Layer (Use Cases)**
- Contains application-specific business logic
- Orchestrates domain entities and infrastructure
- Implements use cases and workflows
- Depends on domain layer only

```go
// Application: Use case implementation
package application

import "project/domain"

type UserService struct {
    repo domain.UserRepository
}

func NewUserService(repo domain.UserRepository) *UserService {
    return &UserService{repo: repo}
}

func (s *UserService) CreateUser(ctx context.Context, email, name string) (*domain.User, error) {
    user := &domain.User{
        ID:    generateID(),
        Email: email,
        Name:  name,
    }
    if err := user.Validate(); err != nil {
        return nil, fmt.Errorf("validation failed: %w", err)
    }
    existing, _ := s.repo.FindByEmail(ctx, email)
    if existing != nil {
        return nil, fmt.Errorf("user with email %s already exists", email)
    }
    if err := s.repo.Save(ctx, user); err != nil {
        return nil, fmt.Errorf("failed to save user: %w", err)
    }
    return user, nil
}
```

**Infrastructure Layer**
- Implements interfaces defined in domain/application layers
- Handles external concerns: database, HTTP clients, file system, etc.
- Depends on domain/application layers
- Contains concrete implementations

```go
// Infrastructure: Database repository implementation
package infrastructure

import (
    "project/domain"
    "database/sql"
)

type postgresUserRepository struct {
    db *sql.DB
}

func NewPostgresUserRepository(db *sql.DB) domain.UserRepository {
    return &postgresUserRepository{db: db}
}

func (r *postgresUserRepository) Save(ctx context.Context, user *domain.User) error {
    query := `INSERT INTO users (id, email, name) VALUES ($1, $2, $3)`
    _, err := r.db.ExecContext(ctx, query, user.ID, user.Email, user.Name)
    return err
}

func (r *postgresUserRepository) FindByID(ctx context.Context, id string) (*domain.User, error) {
    query := `SELECT id, email, name FROM users WHERE id = $1`
    row := r.db.QueryRowContext(ctx, query, id)
    user := &domain.User{}
    err := row.Scan(&user.ID, &user.Email, &user.Name)
    if err == sql.ErrNoRows {
        return nil, fmt.Errorf("user not found")
    }
    return user, err
}

func (r *postgresUserRepository) FindByEmail(ctx context.Context, email string) (*domain.User, error) {
    query := `SELECT id, email, name FROM users WHERE email = $1`
    row := r.db.QueryRowContext(ctx, query, email)
    user := &domain.User{}
    err := row.Scan(&user.ID, &user.Email, &user.Name)
    if err == sql.ErrNoRows {
        return nil, nil
    }
    return user, err
}
```

**Presentation Layer**
- Handles HTTP requests, CLI commands, gRPC, etc.
- Converts between external formats and domain entities
- Depends on application layer
- Thin layer that delegates to application services

```go
// Presentation: HTTP handler
package presentation

import (
    "project/application"
    "github.com/gin-gonic/gin"
)

type UserHandler struct {
    service *application.UserService
}

func NewUserHandler(service *application.UserService) *UserHandler {
    return &UserHandler{service: service}
}

type CreateUserRequest struct {
    Email string `json:"email" binding:"required,email"`
    Name  string `json:"name" binding:"required"`
}

func (h *UserHandler) CreateUser(c *gin.Context) {
    var req CreateUserRequest
    if err := c.ShouldBindJSON(&req); err != nil {
        c.JSON(400, gin.H{"error": err.Error()})
        return
    }
    user, err := h.service.CreateUser(c.Request.Context(), req.Email, req.Name)
    if err != nil {
        c.JSON(500, gin.H{"error": err.Error()})
        return
    }
    c.JSON(201, gin.H{
        "id":    user.ID,
        "email": user.Email,
        "name":  user.Name,
    })
}
```

#### Dependency Injection Pattern

Use constructor functions to inject dependencies, ensuring outer layers provide implementations to inner layers:

```go
// Main: Wire dependencies (dependency injection)
package main

import (
    "project/application"
    "project/infrastructure"
    "project/presentation"
)

func main() {
    db := connectDatabase()
    userRepo := infrastructure.NewPostgresUserRepository(db)
    userService := application.NewUserService(userRepo)
    userHandler := presentation.NewUserHandler(userService)
    
    router := setupRouter(userHandler)
    router.Run(":8080")
}
```

#### Type Declaration Flow

Interfaces must be declared in the file where they are **consumed** (used), not in the file where they are **implemented**. This follows the Dependency Inversion Principle: the consumer defines what it needs, and the provider implements it.

**Rule:** Declare interfaces in the service/consumer file, not in the repository/implementation file.

##### Correct Pattern

```go
// internal/application/shipping/service.go
package shipping

import "context"

// ✅ CORRECT: Interface declared in the service file that uses it
type Repository interface {
    GetShipments(ctx context.Context) ([]*Shipment, error)
    GetShipmentByID(ctx context.Context, id string) (*Shipment, error)
    CreateShipment(ctx context.Context, shipment *Shipment) error
}

type Service struct {
    repo Repository
}

func NewService(repo Repository) *Service {
    return &Service{repo: repo}
}

func (s *Service) ProcessShipments(ctx context.Context) error {
    shipments, err := s.repo.GetShipments(ctx)
    if err != nil {
        return fmt.Errorf("failed to get shipments: %w", err)
    }
    // Process shipments...
    return nil
}
```

```go
// internal/infrastructure/database/postgres/shipping_repository.go
package postgres

import (
    "context"
    "project/internal/application/shipping"
    "database/sql"
)

// ✅ CORRECT: Implementation in infrastructure layer
type shippingRepository struct {
    db *sql.DB
}

func NewShippingRepository(db *sql.DB) shipping.Repository {
    return &shippingRepository{db: db}
}

func (r *shippingRepository) GetShipments(ctx context.Context) ([]*shipping.Shipment, error) {
    // Implementation details
    return nil, nil
}

func (r *shippingRepository) GetShipmentByID(ctx context.Context, id string) (*shipping.Shipment, error) {
    // Implementation details
    return nil, nil
}

func (r *shippingRepository) CreateShipment(ctx context.Context, shipment *shipping.Shipment) error {
    // Implementation details
    return nil
}
```

##### Incorrect Pattern

```go
// ❌ WRONG: Interface declared in the repository implementation file
// internal/infrastructure/database/postgres/shipping_repository.go
package postgres

type Repository interface {
    GetShipments(ctx context.Context) ([]*Shipment, error)
}

type shippingRepository struct {
    db *sql.DB
}

func NewShippingRepository(db *sql.DB) Repository {
    return &shippingRepository{db: db}
}
```

```go
// ❌ WRONG: Service importing from infrastructure layer
// internal/application/shipping/service.go
package shipping

import "project/internal/infrastructure/database/postgres"

type Service struct {
    repo postgres.Repository // Wrong: depends on infrastructure
}
```

##### Why This Matters

1. **Dependency Inversion:** The service (consumer) defines what it needs, not the repository (provider)
2. **Testability:** Easy to create mock implementations for testing
3. **Flexibility:** Can swap implementations without changing the service code
4. **Layer Independence:** Application layer doesn't depend on infrastructure layer details

##### Structure Example

```
shippingservice/
├── service.go          # ✅ Repository interface declared here
└── (no repository.go)   # ✅ No separate interface file needed

infrastructure/
└── database/
    └── postgres/
        └── shipping_repository.go  # ✅ Implementation only
```

#### Anti-Patterns to Avoid

```go
// ❌ BAD: Domain depending on infrastructure
package domain

import "database/sql" // Domain should not know about SQL

type User struct {
    db *sql.DB // Wrong: domain should not have infrastructure dependencies
}

// ❌ BAD: Circular dependency
package application

import "project/presentation" // Application should not depend on presentation

// ❌ BAD: Infrastructure depending on presentation
package infrastructure

import "project/presentation" // Infrastructure should not depend on presentation
</architecture>

<project_structure>
### Standard Go Backend Structure

Organize code by feature/domain with clear layer separation. Use consistent directory patterns that reflect Clean Architecture principles.

#### Recommended Structure

```
project/
├── cmd/
│   └── server/
│       └── main.go                 # Application entry point
├── internal/                       # Private application code
│   ├── domain/                     # Domain layer (entities, interfaces)
│   │   ├── user/
│   │   │   ├── user.go            # User entity
│   │   │   └── repository.go      # Repository interface
│   │   └── product/
│   │       ├── product.go
│   │       └── repository.go
│   ├── application/                # Application layer (use cases)
│   │   ├── user/
│   │   │   └── service.go         # User service (use cases)
│   │   └── product/
│   │       └── service.go
│   ├── infrastructure/             # Infrastructure layer (implementations)
│   │   ├── database/
│   │   │   └── postgres/
│   │   │       ├── connection.go
│   │   │       └── user_repository.go
│   │   ├── cache/
│   │   │   └── redis/
│   │   │       └── cache.go
│   │   └── messaging/
│   │       └── kafka/
│   │           └── producer.go
│   └── presentation/              # Presentation layer (HTTP, CLI, gRPC)
│       ├── http/
│       │   ├── handlers/
│       │   │   ├── user_handler.go
│       │   │   └── product_handler.go
│       │   ├── middleware/
│       │   │   └── auth.go
│       │   └── router.go
│       └── cli/
│           └── commands.go
├── pkg/                            # Public reusable packages
│   ├── logger/
│   ├── validator/
│   └── errors/
├── configs/                        # Configuration files
│   ├── config.yaml
│   └── config.dev.yaml
├── migrations/                     # Database migrations
│   └── 001_create_users.sql
├── scripts/                        # Utility scripts
│   └── migrate.sh
├── docs/                           # Documentation
│   └── api/
├── test/                           # Test utilities and fixtures
│   └── fixtures/
├── go.mod
├── go.sum
├── .golangci.yml
└── README.md
```

#### Domain Layer Structure

```go
// internal/domain/user/user.go
package user

import "context"

type User struct {
    ID    string
    Email string
    Name  string
}

func (u *User) Validate() error {
    // Business validation logic
    return nil
}

// internal/domain/user/repository.go
package user

import "context"

type Repository interface {
    Save(ctx context.Context, user *User) error
    FindByID(ctx context.Context, id string) (*User, error)
    FindByEmail(ctx context.Context, email string) (*User, error)
}
```

#### Application Layer Structure

```go
// internal/application/user/service.go
package user

import (
    "context"
    "project/internal/domain/user"
)

type Service struct {
    repo user.Repository
}

func NewService(repo user.Repository) *Service {
    return &Service{repo: repo}
}

func (s *Service) CreateUser(ctx context.Context, email, name string) (*user.User, error) {
    // Use case implementation
    return nil, nil
}
```

#### Infrastructure Layer Structure

```go
// internal/infrastructure/database/postgres/user_repository.go
package postgres

import (
    "context"
    "database/sql"
    "project/internal/domain/user"
)

type userRepository struct {
    db *sql.DB
}

func NewUserRepository(db *sql.DB) user.Repository {
    return &userRepository{db: db}
}

func (r *userRepository) Save(ctx context.Context, u *user.User) error {
    // Database implementation
    return nil
}
```

#### Presentation Layer Structure

```go
// internal/presentation/http/handlers/user_handler.go
package handlers

import (
    "project/internal/application/user"
    "github.com/gin-gonic/gin"
)

type UserHandler struct {
    service *user.Service
}

func NewUserHandler(service *user.Service) *UserHandler {
    return &UserHandler{service: service}
}

func (h *UserHandler) CreateUser(c *gin.Context) {
    // HTTP handler implementation
}
```

#### Main Entry Point

```go
// cmd/server/main.go
package main

import (
    "project/internal/application/user"
    "project/internal/infrastructure/database/postgres"
    "project/internal/presentation/http/handlers"
    "project/internal/presentation/http"
)

func main() {
    db := postgres.Connect()
    userRepo := postgres.NewUserRepository(db)
    userService := user.NewService(userRepo)
    userHandler := handlers.NewUserHandler(userService)
    
    router := http.NewRouter(userHandler)
    router.Run(":8080")
}
```

#### Package Naming Conventions

- Use lowercase, single-word package names
- Avoid generic names like `util`, `common`, `helpers`
- Use domain-specific names: `user`, `product`, `order`
- Keep package names short and descriptive

#### File Organization Rules

1. **One package per directory:** Each directory contains one package
2. **Separate interfaces:** Put interfaces in separate files when used across packages
3. **Group related code:** Keep related types, functions, and methods together
4. **Clear boundaries:** Maintain clear separation between layers

## Basic Tech Stack

### Core Framework and Libraries

#### HTTP Framework

**MUST use:** `gin-gonic/gin` for HTTP APIs

```go
import "github.com/gin-gonic/gin"

func setupRouter() *gin.Engine {
    router := gin.Default()
    router.GET("/health", healthCheck)
    router.POST("/users", createUser)
    return router
}
```

**Rationale:** Gin is lightweight, fast, and provides excellent middleware support. It's the standard choice for Go HTTP APIs.

#### Validation

**MUST use:** `go-playground/validator/v10` for input validation

```go
import "github.com/go-playground/validator/v10"

type CreateUserRequest struct {
    Email string `json:"email" binding:"required,email"`
    Name  string `json:"name" binding:"required,min=2,max=100"`
    Age   int    `json:"age" binding:"required,min=18,max=120"`
}

func validateRequest(req *CreateUserRequest) error {
    validate := validator.New()
    return validate.Struct(req)
}
```

**Rationale:** Validator provides comprehensive validation rules and integrates seamlessly with Gin's binding.

#### CLI Framework

**MUST use:** `spf13/cobra` for command-line interfaces

```go
import "github.com/spf13/cobra"

var rootCmd = &cobra.Command{
    Use:   "app",
    Short: "Application CLI",
}

var migrateCmd = &cobra.Command{
    Use:   "migrate",
    Short: "Run database migrations",
    Run: func(cmd *cobra.Command, args []string) {
        // Migration logic
    },
}

func init() {
    rootCmd.AddCommand(migrateCmd)
}
```

**Rationale:** Cobra is the de facto standard for CLI applications in Go, providing powerful command structure and flag parsing.

#### API Documentation

**MUST use:** `swaggo/swag` for API documentation (Swagger/OpenAPI)

```go
// @title           User API
// @version         1.0
// @description     User management API
// @host            localhost:8080
// @BasePath        /api/v1

// @Summary         Create user
// @Description     Create a new user
// @Tags            users
// @Accept          json
// @Produce         json
// @Param           user body CreateUserRequest true "User data"
// @Success         201 {object} UserResponse
// @Router          /users [post]
func CreateUser(c *gin.Context) {
    // Handler implementation
}
```

**Rationale:** Swag generates OpenAPI documentation from code comments, ensuring documentation stays in sync with code.

### Database

#### SQL Database

**Recommended:** `database/sql` with PostgreSQL driver (`lib/pq` or `pgx`)

```go
import (
    "database/sql"
    _ "github.com/lib/pq"
)

func connectDB() (*sql.DB, error) {
    connStr := "postgres://user:pass@localhost/dbname?sslmode=disable"
    db, err := sql.Open("postgres", connStr)
    if err != nil {
        return nil, err
    }
    if err := db.Ping(); err != nil {
        return nil, err
    }
    return db, nil
}
```

**Rationale:** Standard library `database/sql` provides a clean interface. PostgreSQL is the recommended database for production systems.

#### Migration Tools

**Recommended:** `golang-migrate/migrate` for database migrations

```go
import "github.com/golang-migrate/migrate/v4"

func runMigrations(dbURL string) error {
    m, err := migrate.New("file://migrations", dbURL)
    if err != nil {
        return err
    }
    return m.Up()
}
```

### Configuration Management

**Recommended:** `spf13/viper` for configuration management

```go
import "github.com/spf13/viper"

func loadConfig() (*Config, error) {
    viper.SetConfigName("config")
    viper.SetConfigType("yaml")
    viper.AddConfigPath(".")
    viper.AddConfigPath("./configs")
    
    if err := viper.ReadInConfig(); err != nil {
        return nil, err
    }
    
    var config Config
    if err := viper.Unmarshal(&config); err != nil {
        return nil, err
    }
    return &config, nil
}
```

### Logging

**Use project logger:** Always use `logger.FromContext(ctx)` pattern (see golang-engineer skill)

```go
import "project/pkg/logger"

func processRequest(ctx context.Context) error {
    log := logger.FromContext(ctx)
    log.Info("processing request", "request_id", requestID)
    return nil
}
```

### Testing

**MUST use:** `stretchr/testify` for assertions and test suites (see golang-testing skill)

```go
import (
    "testing"
    "github.com/stretchr/testify/assert"
    "github.com/stretchr/testify/require"
)

func TestUserService_CreateUser(t *testing.T) {
    // Test implementation
    assert.NoError(t, err)
    require.NotNil(t, user)
}
```

### Additional Recommended Libraries

- **Context:** Use `context.Context` for request-scoped values and cancellation (standard library)
- **Errors:** Use `fmt.Errorf` for error wrapping (standard library)
- **Time:** Use `time` package for time operations (standard library)
- **JSON:** Use `encoding/json` for JSON marshaling/unmarshaling (standard library)
- **HTTP Client:** Use `net/http` for HTTP clients (standard library)

### Tech Stack Summary

| Category | Library | Purpose |
|----------|---------|---------|
| HTTP Framework | `gin-gonic/gin` | HTTP API server |
| Validation | `go-playground/validator/v10` | Input validation |
| CLI | `spf13/cobra` | Command-line interface |
| Documentation | `swaggo/swag` | API documentation |
| Database | `database/sql` + `lib/pq` | PostgreSQL driver |
| Migrations | `golang-migrate/migrate` | Database migrations |
| Configuration | `spf13/viper` | Configuration management |
| Testing | `stretchr/testify` | Test assertions |
| Logging | Project logger | Structured logging |

### Stack Selection Principles

1. **Prefer Standard Library:** Use Go standard library when possible
2. **Proven Libraries:** Choose well-maintained, widely-used libraries
3. **Minimal Dependencies:** Avoid unnecessary dependencies
4. **Consistency:** Use the same libraries across the project
5. **Performance:** Consider performance implications for high-throughput systems
</tech_stack>

## ⚠️ Critical Reminder

**Always follow the `<critical_validations>` section at the top of this document. These architectural principles, project structure patterns, and tech stack decisions are mandatory and must be enforced in all backend engineering work.**

