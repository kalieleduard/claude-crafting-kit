---
name: code-quality
description: Expert in code design standards including SOLID principles, Clean Code patterns (KISS, YAGNI, DRY, TDA), and pragmatic software design. **ALWAYS use when designing ANY classes/modules, implementing features, fixing bugs, refactoring code, or writing functions.** Use proactively to ensure proper design, separation of concerns, simplicity, and maintainability. Examples - "create class", "design module", "implement feature", "refactor code", "fix bug", "is this too complex", "apply SOLID", "keep it simple", "avoid over-engineering".
---

# Code Quality Standards and Principles

<critical_validations>
- SOLID Principles: Apply Single Responsibility, Open/Closed, Liskov Substitution, Interface Segregation, and Dependency Inversion principles to all class and module designs.
- Clean Code Principles: Follow KISS (Keep It Simple), YAGNI (You Aren't Gonna Need It), DRY (Don't Repeat Yourself), and TDA (Tell, Don't Ask) in all implementations.
- Small Functions: Keep functions small, focused on a single responsibility, and readable at a glance.
- Meaningful Names: Use intention-revealing, pronounceable, and searchable names for all variables, functions, and classes.
- Single Level of Abstraction: Maintain consistent abstraction levels within functions; high-level functions should call lower-level functions.
- Early Returns: Use guard clauses and early returns to reduce nesting and improve code readability.
- Anti-Patterns: Avoid god objects, magic numbers, long parameter lists, feature envy, dead code, and explanatory comments.
- Go Type Grouping: Group related interface and type declarations using `type()` blocks instead of declaring each type separately.
</critical_validations>

This document outlines essential code quality principles including SOLID, Clean Code practices, and common anti-patterns to avoid. **Always apply these principles when writing, reviewing, or refactoring code.**

## Related Skills

→ Use `golang-engineer` skill for Go-specific implementation patterns, error handling, and idiomatic Go code when applying these principles to Go projects

→ Use `golang-testing` skill for writing tests that follow these code quality principles and ensuring proper test coverage

## SOLID Principles

SOLID is an acronym for five object-oriented design principles that make software designs more understandable, flexible, and maintainable.

### Single Responsibility Principle (SRP)

**Definition:** A class or module should have one, and only one, reason to change. It should have only one job or responsibility.

**Why it matters:** When a class has multiple responsibilities, changes to one responsibility can affect the others, making the code harder to maintain and test.

#### Bad Example
```go
type User struct {
    name  string
    email string
}

func NewUser(name, email string) *User {
    return &User{name: name, email: email}
}

func (u *User) GetName() string {
    return u.name
}

func (u *User) GetEmail() string {
    return u.email
}

// Database responsibility - violates SRP
func (u *User) SaveToDatabase() error {
    // Database save logic
    return nil
}

// Email responsibility - violates SRP
func (u *User) SendEmail(subject, body string) error {
    // Email sending logic
    return nil
}

// Validation responsibility - violates SRP
func (u *User) ValidateEmail() bool {
    return strings.Contains(u.email, "@")
}
```

#### Good Example
```go
// User entity - only responsible for user data
type User struct {
    name  string
    email string
}

func NewUser(name, email string) *User {
    return &User{name: name, email: email}
}

func (u *User) GetName() string {
    return u.name
}

func (u *User) GetEmail() string {
    return u.email
}

// User repository - responsible for persistence
type UserRepository interface {
    Save(user *User) error
    FindByID(id string) (*User, error)
}

type userRepository struct {
    // database connection
}

func (r *userRepository) Save(user *User) error {
    // Database save logic
    return nil
}

func (r *userRepository) FindByID(id string) (*User, error) {
    // Database find logic
    return nil, nil
}

// Email service - responsible for sending emails
type EmailService interface {
    SendEmail(to, subject, body string) error
}

type emailService struct {
    // email client
}

func (s *emailService) SendEmail(to, subject, body string) error {
    // Email sending logic
    return nil
}

// User validator - responsible for validation
type UserValidator struct{}

func (v *UserValidator) ValidateEmail(email string) bool {
    return strings.Contains(email, "@") && strings.Contains(email, ".")
}

func (v *UserValidator) ValidateUser(user *User) bool {
    return v.ValidateEmail(user.GetEmail()) && len(user.GetName()) > 0
}
```

### Open/Closed Principle (OCP)

**Definition:** Software entities (classes, modules, functions) should be open for extension but closed for modification. You should be able to add new functionality without changing existing code.

**Why it matters:** Prevents breaking changes to existing code when adding new features, reducing bugs and maintenance costs.

#### Bad Example
```go
type ShapeType string

const (
    RectangleType ShapeType = "rectangle"
    CircleType    ShapeType = "circle"
    TriangleType  ShapeType = "triangle"
)

type Shape struct {
    Type   ShapeType
    Width  float64
    Height float64
    Radius float64
    Base   float64
}

type AreaCalculator struct{}

func (c *AreaCalculator) CalculateArea(shape Shape) (float64, error) {
    switch shape.Type {
    case RectangleType:
        return shape.Width * shape.Height, nil
    case CircleType:
        return math.Pi * shape.Radius * shape.Radius, nil
    case TriangleType:
        return (shape.Base * shape.Height) / 2, nil
    default:
        // Adding new shapes requires modifying this function
        return 0, fmt.Errorf("unknown shape type")
    }
}
```

#### Good Example
```go
// Shape interface - closed for modification
type Shape interface {
    GetArea() float64
}

// Concrete implementations - open for extension
type Rectangle struct {
    width  float64
    height float64
}

func NewRectangle(width, height float64) *Rectangle {
    return &Rectangle{width: width, height: height}
}

func (r *Rectangle) GetArea() float64 {
    return r.width * r.height
}

type Circle struct {
    radius float64
}

func NewCircle(radius float64) *Circle {
    return &Circle{radius: radius}
}

func (c *Circle) GetArea() float64 {
    return math.Pi * c.radius * c.radius
}

type Triangle struct {
    base   float64
    height float64
}

func NewTriangle(base, height float64) *Triangle {
    return &Triangle{base: base, height: height}
}

func (t *Triangle) GetArea() float64 {
    return (t.base * t.height) / 2
}

// Calculator doesn't need to change when adding new shapes
type AreaCalculator struct{}

func (c *AreaCalculator) CalculateArea(shape Shape) float64 {
    return shape.GetArea()
}
```

### Liskov Substitution Principle (LSP)

**Definition:** Objects of a superclass should be replaceable with objects of its subclasses without breaking the application. Subtypes must be substitutable for their base types.

**Why it matters:** Ensures that inheritance hierarchies are correctly designed and that polymorphism works as expected.

#### Bad Example
```go
type Rectangle struct {
    width  float64
    height float64
}

func NewRectangle(width, height float64) *Rectangle {
    return &Rectangle{width: width, height: height}
}

func (r *Rectangle) SetWidth(width float64) {
    r.width = width
}

func (r *Rectangle) SetHeight(height float64) {
    r.height = height
}

func (r *Rectangle) GetArea() float64 {
    return r.width * r.height
}

type Square struct {
    *Rectangle
}

func NewSquare(side float64) *Square {
    return &Square{Rectangle: NewRectangle(side, side)}
}

func (s *Square) SetWidth(width float64) {
    s.width = width
    s.height = width // Violates LSP - changes behavior
}

func (s *Square) SetHeight(height float64) {
    s.width = height // Violates LSP - changes behavior
    s.height = height
}

// This code breaks when Square is substituted for Rectangle
func testRectangle(rect *Rectangle) {
    rect.SetWidth(5)
    rect.SetHeight(4)
    // Expects area to be 20, but Square would make it 16
    fmt.Println(rect.GetArea()) // Unexpected behavior!
}
```

#### Good Example
```go
// Shape interface
type Shape interface {
    GetArea() float64
}

type Rectangle struct {
    width  float64
    height float64
}

func NewRectangle(width, height float64) *Rectangle {
    return &Rectangle{width: width, height: height}
}

func (r *Rectangle) GetArea() float64 {
    return r.width * r.height
}

type Square struct {
    side float64
}

func NewSquare(side float64) *Square {
    return &Square{side: side}
}

func (s *Square) GetArea() float64 {
    return s.side * s.side
}

// Both can be substituted without issues
func calculateTotalArea(shapes []Shape) float64 {
    total := 0.0
    for _, shape := range shapes {
        total += shape.GetArea()
    }
    return total
}

func main() {
    shapes := []Shape{
        NewRectangle(5, 4),
        NewSquare(5),
        NewRectangle(3, 2),
    }
    fmt.Println(calculateTotalArea(shapes)) // Works correctly
}
```

### Interface Segregation Principle (ISP)

**Definition:** Clients should not be forced to depend on interfaces they do not use. Many client-specific interfaces are better than one general-purpose interface.

**Why it matters:** Prevents classes from being forced to implement methods they don't need, reducing coupling and improving maintainability.

#### Bad Example
```go
type Worker interface {
    Work()
    Eat()
    Sleep()
}

type HumanWorker struct{}

func (h *HumanWorker) Work() {
    fmt.Println("Human working")
}

func (h *HumanWorker) Eat() {
    fmt.Println("Human eating")
}

func (h *HumanWorker) Sleep() {
    fmt.Println("Human sleeping")
}

type RobotWorker struct{}

func (r *RobotWorker) Work() {
    fmt.Println("Robot working")
}

func (r *RobotWorker) Eat() {
    panic("Robots cannot eat") // Forced to implement unused method
}

func (r *RobotWorker) Sleep() {
    panic("Robots cannot sleep") // Forced to implement unused method
}
```

#### Good Example
```go
// Segregated interfaces
type Workable interface {
    Work()
}

type Eatable interface {
    Eat()
}

type Sleepable interface {
    Sleep()
}

type HumanWorker struct{}

func (h *HumanWorker) Work() {
    fmt.Println("Human working")
}

func (h *HumanWorker) Eat() {
    fmt.Println("Human eating")
}

func (h *HumanWorker) Sleep() {
    fmt.Println("Human sleeping")
}

type RobotWorker struct{}

func (r *RobotWorker) Work() {
    fmt.Println("Robot working")
}
// No need to implement Eat() or Sleep()

type Manager struct{}

func (m *Manager) ManageWorkers(workers []Workable) {
    for _, worker := range workers {
        worker.Work()
    }
}
```

### Dependency Inversion Principle (DIP)

**Definition:** High-level modules should not depend on low-level modules. Both should depend on abstractions. Abstractions should not depend on details; details should depend on abstractions.

**Why it matters:** Reduces coupling between modules, making the system more flexible and easier to test and maintain.

#### Bad Example
```go
// High-level module depends on low-level module
type MySQLDatabase struct{}

func (db *MySQLDatabase) Save(data string) error {
    fmt.Printf("Saving %s to MySQL\n", data)
    return nil
}

type UserService struct {
    database *MySQLDatabase // Direct dependency on concrete type
}

func NewUserService() *UserService {
    return &UserService{
        database: &MySQLDatabase{}, // Tight coupling
    }
}

func (s *UserService) SaveUser(name string) error {
    return s.database.Save(name)
}
```

#### Good Example
```go
// Abstraction (interface)
type Database interface {
    Save(data string) error
}

// Low-level module depends on abstraction
type MySQLDatabase struct{}

func (db *MySQLDatabase) Save(data string) error {
    fmt.Printf("Saving %s to MySQL\n", data)
    return nil
}

type PostgreSQLDatabase struct{}

func (db *PostgreSQLDatabase) Save(data string) error {
    fmt.Printf("Saving %s to PostgreSQL\n", data)
    return nil
}

// High-level module depends on abstraction
type UserService struct {
    database Database // Depends on abstraction
}

func NewUserService(database Database) *UserService {
    return &UserService{database: database}
}

func (s *UserService) SaveUser(name string) error {
    return s.database.Save(name)
}

// Dependency injection at composition root
func main() {
    database := &MySQLDatabase{}
    userService := NewUserService(database)
    _ = userService
}
```

## Go-Specific Best Practices

### Group Type Declarations

**Principle:** Group related interface and type declarations together using `type()` blocks instead of declaring each type separately. This improves readability, reduces repetition, and groups related types logically.

**Why it matters:** Grouped type declarations make it easier to see related types at a glance, reduce visual clutter, and follow Go idioms for organizing type definitions.

#### Bad Example
```go
type UserRepository interface {
    Save(user *User) error
    FindByID(id string) (*User, error)
}

type EmailService interface {
    SendEmail(to, subject, body string) error
}

type UserValidator interface {
    ValidateEmail(email string) bool
    ValidateUser(user *User) bool
}

type UserID string

type Email string

type UserStatus int
```

#### Good Example
```go
type (
    UserRepository interface {
        Save(user *User) error
        FindByID(id string) (*User, error)
    }

    EmailService interface {
        SendEmail(to, subject, body string) error
    }

    UserValidator interface {
        ValidateEmail(email string) bool
        ValidateUser(user *User) bool
    }

    UserID     string
    Email      string
    UserStatus int
)
```

### Guidelines for Grouping Types

1. **Group related types together:**
   ```go
   // Good - Related interfaces grouped
   type (
       Reader interface {
           Read(p []byte) (n int, err error)
       }

       Writer interface {
           Write(p []byte) (n int, err error)
       }

       Closer interface {
           Close() error
       }
   )
   ```

2. **Group type aliases and custom types:**
   ```go
   // Good - Related custom types grouped
   type (
       UserID     string
       OrderID    string
       ProductID  string
       CustomerID string
   )
   ```

3. **Separate unrelated groups:**
   ```go
   // Good - Separate groups for different concerns
   type (
       // Repository interfaces
       UserRepository interface {
           Save(user *User) error
       }

       OrderRepository interface {
           Save(order *Order) error
       }
   )

   type (
       // Domain types
       UserID  string
       OrderID string
   )
   ```

4. **Avoid mixing interfaces and structs unnecessarily:**
   ```go
   // Bad - Mixing interfaces and structs in same group
   type (
       UserRepository interface {
           Save(user *User) error
       }

       User struct {
           ID   string
           Name string
       }
   )

   // Good - Separate groups for interfaces and structs
   type (
       UserRepository interface {
           Save(user *User) error
       }
   )

   type User struct {
       ID   string
       Name string
   }
   ```

## Clean Code Principles

### KISS (Keep It Simple, Stupid)

**Definition:** Keep code simple and straightforward. Avoid unnecessary complexity. Simple solutions are easier to understand, maintain, and debug.

#### Bad Example
```go
func processUserData(users []interface{}) []interface{} {
    var result []interface{}
    for _, u := range users {
        userMap := u.(map[string]interface{})
        processed := map[string]interface{}{
            "id":        userMap["id"],
            "name":      userMap["name"],
            "processed": true,
            "timestamp": time.Now().Format(time.RFC3339),
            "metadata": map[string]interface{}{
                "source":  "api",
                "version": "1.0",
                "checksum": base64.StdEncoding.EncodeToString([]byte(fmt.Sprintf("%v", u)))[:16],
            },
        }
        if checksum, ok := processed["metadata"].(map[string]interface{})["checksum"].(string); ok && len(checksum) > 0 {
            result = append(result, processed)
        }
    }
    // Complex sorting and deduplication logic...
    return result
}
```

#### Good Example
```go
type User struct {
    ID        string
    Name      string
    Processed bool
    Timestamp time.Time
}

func processUserData(users []User) []User {
    processedUsers := make([]User, len(users))
    for i, user := range users {
        processedUsers[i] = User{
            ID:        user.ID,
            Name:      user.Name,
            Processed: true,
            Timestamp: time.Now(),
        }
    }
    return removeDuplicates(processedUsers)
}

func removeDuplicates(users []User) []User {
    seen := make(map[string]bool)
    var result []User
    for _, user := range users {
        if !seen[user.ID] {
            seen[user.ID] = true
            result = append(result, user)
        }
    }
    return result
}
```

### YAGNI (You Aren't Gonna Need It)

**Definition:** Don't implement functionality until it's actually needed. Avoid speculative code and premature optimization.

#### Bad Example
```go
type UserService struct {
    repo UserRepository
}

// Implementing features that aren't needed yet
func (s *UserService) GetUser(ctx context.Context, id string) (*User, error) {
    // Current requirement
    return s.repo.FindByID(ctx, id)
}

func (s *UserService) GetUserWithCache(ctx context.Context, id string) (*User, error) {
    // Not needed yet - speculative
    return nil, nil
}

func (s *UserService) GetUserWithAnalytics(ctx context.Context, id string) (*User, error) {
    // Not needed yet - speculative
    return nil, nil
}

func (s *UserService) GetUserWithMetrics(ctx context.Context, id string) (*User, error) {
    // Not needed yet - speculative
    return nil, nil
}

func (s *UserService) GetUserOptimized(ctx context.Context, id string) (*User, error) {
    // Premature optimization
    return nil, nil
}
```

#### Good Example
```go
type UserService struct {
    repo UserRepository
}

// Only implement what's needed now
func (s *UserService) GetUser(ctx context.Context, id string) (*User, error) {
    // Simple, focused implementation
    return s.repo.FindByID(ctx, id)
}

// Add features only when requirements emerge
// func (s *UserService) GetUserWithCache(ctx context.Context, id string) (*User, error) { ... } // Add when needed
```

### DRY (Don't Repeat Yourself)

**Definition:** Every piece of knowledge should have a single, unambiguous representation within a system. Avoid code duplication.

#### Bad Example
```go
func calculateEmployeeSalary(employee Employee) float64 {
    baseSalary := employee.BaseSalary
    bonus := employee.Bonus
    tax := (baseSalary + bonus) * 0.2
    return baseSalary + bonus - tax
}

func calculateManagerSalary(manager Manager) float64 {
    baseSalary := manager.BaseSalary
    bonus := manager.Bonus
    tax := (baseSalary + bonus) * 0.2 // Duplicated logic
    return baseSalary + bonus - tax
}

func calculateExecutiveSalary(executive Executive) float64 {
    baseSalary := executive.BaseSalary
    bonus := executive.Bonus
    tax := (baseSalary + bonus) * 0.2 // Duplicated logic
    return baseSalary + bonus - tax
}
```

#### Good Example
```go
func calculateTax(income float64) float64 {
    return income * 0.2
}

func calculateNetSalary(baseSalary, bonus float64) float64 {
    grossSalary := baseSalary + bonus
    tax := calculateTax(grossSalary)
    return grossSalary - tax
}

func calculateEmployeeSalary(employee Employee) float64 {
    return calculateNetSalary(employee.BaseSalary, employee.Bonus)
}

func calculateManagerSalary(manager Manager) float64 {
    return calculateNetSalary(manager.BaseSalary, manager.Bonus)
}

func calculateExecutiveSalary(executive Executive) float64 {
    return calculateNetSalary(executive.BaseSalary, executive.Bonus)
}
```

### TDA (Tell, Don't Ask)

**Definition:** Objects should tell other objects what to do rather than asking about their state and making decisions for them. Encapsulate behavior within objects.

#### Bad Example
```go
type BankAccount struct {
    balance float64
}

func NewBankAccount(balance float64) *BankAccount {
    return &BankAccount{balance: balance}
}

func (a *BankAccount) GetBalance() float64 {
    return a.balance
}

func (a *BankAccount) SetBalance(amount float64) {
    a.balance = amount
}

// Client code asks about state and makes decisions
func withdraw(account *BankAccount, amount float64) error {
    balance := account.GetBalance() // Asking
    if balance >= amount {          // Making decision for the account
        account.SetBalance(balance - amount)
        return nil
    }
    return fmt.Errorf("insufficient funds")
}
```

#### Good Example
```go
type BankAccount struct {
    balance float64
}

func NewBankAccount(balance float64) *BankAccount {
    return &BankAccount{balance: balance}
}

func (a *BankAccount) Withdraw(amount float64) error {
    if a.balance < amount {
        return fmt.Errorf("insufficient funds")
    }
    a.balance -= amount
    return nil
}

func (a *BankAccount) GetBalance() float64 {
    return a.balance
}

// Client code tells the account what to do
func withdraw(account *BankAccount, amount float64) error {
    return account.Withdraw(amount) // Telling
}
```

## Small Functions

**Principle:** Functions should be small and do one thing. They should fit on a screen and be easy to understand at a glance.

### Bad Example
```go
func processOrder(order *Order) error {
    // 50+ lines of code doing multiple things
    if len(order.Items) == 0 {
        return fmt.Errorf("order must have items")
    }
    total := 0.0
    for _, item := range order.Items {
        if item.Quantity <= 0 {
            return fmt.Errorf("invalid quantity")
        }
        if item.Price < 0 {
            return fmt.Errorf("invalid price")
        }
        total += item.Price * float64(item.Quantity)
    }
    if order.Customer.CreditLimit < total {
        return fmt.Errorf("credit limit exceeded")
    }
    order.Customer.CreditLimit -= total
    tax := total * 0.1
    finalTotal := total + tax
    order.Total = finalTotal
    order.Status = "processed"
    order.ProcessedAt = time.Now()
    database.Save(order)
    emailService.Send(order.Customer.Email, "Order processed", fmt.Sprintf("Your order total is %.2f", finalTotal))
    inventoryService.Update(order.Items)
    analyticsService.Track("order_processed", map[string]interface{}{
        "orderId": order.ID,
        "total":   finalTotal,
    })
    return nil
}
```

### Good Example
```go
func processOrder(order *Order) error {
    if err := validateOrder(order); err != nil {
        return err
    }
    total, err := calculateTotal(order.Items)
    if err != nil {
        return err
    }
    if err := validateCreditLimit(order.Customer, total); err != nil {
        return err
    }
    applyOrderToCustomer(order.Customer, total)
    finalizeOrder(order, total)
    if err := persistOrder(order); err != nil {
        return err
    }
    notifyCustomer(order)
    updateInventory(order.Items)
    trackAnalytics(order)
    return nil
}

func validateOrder(order *Order) error {
    if len(order.Items) == 0 {
        return fmt.Errorf("order must have items")
    }
    return nil
}

func calculateTotal(items []OrderItem) (float64, error) {
    total := 0.0
    for _, item := range items {
        if err := validateItem(item); err != nil {
            return 0, err
        }
        total += item.Price * float64(item.Quantity)
    }
    return total, nil
}

func validateItem(item OrderItem) error {
    if item.Quantity <= 0 {
        return fmt.Errorf("invalid quantity")
    }
    if item.Price < 0 {
        return fmt.Errorf("invalid price")
    }
    return nil
}

func validateCreditLimit(customer *Customer, total float64) error {
    if customer.CreditLimit < total {
        return fmt.Errorf("credit limit exceeded")
    }
    return nil
}

func applyOrderToCustomer(customer *Customer, total float64) {
    customer.CreditLimit -= total
}

func finalizeOrder(order *Order, total float64) {
    tax := calculateTax(total)
    order.Total = total + tax
    order.Status = "processed"
    order.ProcessedAt = time.Now()
}

func calculateTax(total float64) float64 {
    return total * 0.1
}

func persistOrder(order *Order) error {
    return database.Save(order)
}

func notifyCustomer(order *Order) {
    emailService.Send(
        order.Customer.Email,
        "Order processed",
        fmt.Sprintf("Your order total is %.2f", order.Total),
    )
}

func updateInventory(items []OrderItem) {
    inventoryService.Update(items)
}

func trackAnalytics(order *Order) {
    analyticsService.Track("order_processed", map[string]interface{}{
        "orderId": order.ID,
        "total":   order.Total,
    })
}
```

## Meaningful Names

**Principle:** Use intention-revealing names. A name should tell you why it exists, what it does, and how it's used.

### Bad Example
```go
d := 86400000 // What is this?
time.AfterFunc(time.Duration(d)*time.Millisecond, restart)

func getData() interface{} {
    // What data?
    return nil
}

func process(x, y interface{}) interface{} {
    // What does this process?
    return nil
}

u := getUser()
s := getSubscription()
t := charge(u, s)
```

### Good Example
```go
const millisecondsPerDay = 24 * 60 * 60 * 1000
time.AfterFunc(time.Duration(millisecondsPerDay)*time.Millisecond, restart)

func getUserProfile() *UserProfile {
    // Clear what data is returned
    return nil
}

func calculateOrderTotal(order *Order) float64 {
    // Clear what it processes and returns
    return 0.0
}

user := getUser()
subscription := getSubscription()
transaction := charge(user, subscription)
```

### Naming Guidelines

1. **Use pronounceable names:**
   ```go
   // Bad
   genymdhms := time.Now()
   
   // Good
   generationTimestamp := time.Now()
   ```

2. **Use searchable names:**
   ```go
   // Bad
   for i := 0; i < 5; i++ { }
   
   // Good
   const maxRetryAttempts = 5
   for i := 0; i < maxRetryAttempts; i++ { }
   ```

3. **Avoid mental mapping:**
   ```go
   // Bad
   locations := []string{"Austin", "New York"}
   for _, l := range locations {
       dispatch(l)
   }
   
   // Good
   locations := []string{"Austin", "New York"}
   for _, location := range locations {
       dispatch(location)
   }
   ```

4. **Use consistent vocabulary:**
   ```go
   // Bad
   func getUserInfo() *User { return nil }
   func getUserDetails() *User { return nil }
   func getUserData() *User { return nil }
   
   // Good
   func getUser() *User { return nil }
   ```

## Single Level of Abstraction

**Principle:** Functions should operate at a single level of abstraction. High-level functions should call lower-level functions, not mix levels.

### Bad Example
```go
func parseCode(code string) SyntaxTree {
    // High-level: parsing code
    regexes := []*regexp.Regexp{ /* ... */ }
    
    // Low-level: string manipulation
    statements := strings.Split(code, " ")
    var tokens []Token
    
    // Low-level: regex matching
    for _, regex := range regexes {
        for _, statement := range statements {
            if regex.MatchString(statement) {
                tokens = append(tokens, Token{Type: "token", Value: statement})
            }
        }
    }
    
    // Low-level: building AST
    var ast SyntaxTree
    for _, token := range tokens {
        ast = append(ast, Node{Type: "node", Token: token})
    }
    
    // High-level: returning result
    return ast
}
```

### Good Example
```go
func parseCode(code string) SyntaxTree {
    tokens := tokenize(code)
    syntaxTree := parse(tokens)
    return syntaxTree
}

func tokenize(code string) []Token {
    regexes := []*regexp.Regexp{ /* ... */ }
    statements := strings.Split(code, " ")
    var tokens []Token
    
    for _, regex := range regexes {
        for _, statement := range statements {
            if regex.MatchString(statement) {
                tokens = append(tokens, Token{Type: "token", Value: statement})
            }
        }
    }
    
    return tokens
}

func parse(tokens []Token) SyntaxTree {
    var syntaxTree SyntaxTree
    for _, token := range tokens {
        syntaxTree = append(syntaxTree, Node{Type: "node", Token: token})
    }
    return syntaxTree
}
```

## Early Returns

**Principle:** Use guard clauses and early returns to reduce nesting and improve readability. Handle error cases first, then proceed with the happy path.

### Bad Example
```go
func processUser(user *User) (string, error) {
    if user != nil {
        if user.Email != nil {
            if len(*user.Email) > 0 {
                if user.IsActive {
                    return fmt.Sprintf("Processing %s", *user.Email), nil
                } else {
                    return "", fmt.Errorf("user is not active")
                }
            } else {
                return "", fmt.Errorf("email is empty")
            }
        } else {
            return "", fmt.Errorf("email is null")
        }
    } else {
        return "", fmt.Errorf("user is null")
    }
}
```

### Good Example
```go
func processUser(user *User) (string, error) {
    if user == nil {
        return "", fmt.Errorf("user is null")
    }
    
    if user.Email == nil {
        return "", fmt.Errorf("email is null")
    }
    
    if len(*user.Email) == 0 {
        return "", fmt.Errorf("email is empty")
    }
    
    if !user.IsActive {
        return "", fmt.Errorf("user is not active")
    }
    
    return fmt.Sprintf("Processing %s", *user.Email), nil
}
```

### Benefits of Early Returns

1. **Reduces cognitive load** - No need to keep track of nested conditions
2. **Improves readability** - Linear flow is easier to follow
3. **Reduces errors** - Less chance of missing closing braces
4. **Makes intent clear** - Error cases are handled upfront

## Common Anti-Patterns

### 1. God Object / God Class

**Problem:** A class that knows too much or does too much.

```go
// Bad
type UserManager struct{}

// User management
func (m *UserManager) CreateUser() {}

func (m *UserManager) UpdateUser() {}

func (m *UserManager) DeleteUser() {}

// Email management
func (m *UserManager) SendWelcomeEmail() {}

func (m *UserManager) SendPasswordReset() {}

// Database management
func (m *UserManager) ConnectToDatabase() {}

func (m *UserManager) QueryDatabase() {}

// Validation
func (m *UserManager) ValidateEmail() {}

func (m *UserManager) ValidatePassword() {}

// Logging
func (m *UserManager) LogActivity() {}

func (m *UserManager) LogError() {}
```

**Solution:** Split into focused classes.

```go
// Good
type UserService struct {
    userRepository UserRepository
    emailService   EmailService
    validator      UserValidator
}

func NewUserService(repo UserRepository, email EmailService, validator UserValidator) *UserService {
    return &UserService{
        userRepository: repo,
        emailService:   email,
        validator:      validator,
    }
}

func (s *UserService) CreateUser(userData UserData) (*User, error) {
    if err := s.validator.Validate(userData); err != nil {
        return nil, err
    }
    user, err := s.userRepository.Save(userData)
    if err != nil {
        return nil, err
    }
    if err := s.emailService.SendWelcomeEmail(user); err != nil {
        return nil, err
    }
    return user, nil
}
```

### 2. Magic Numbers

**Problem:** Using numbers without explanation.

```go
// Bad
if user.Age < 18 { }
time.AfterFunc(86400000*time.Millisecond, callback)
if len(items) > 100 { }
```

**Solution:** Use named constants.

```go
// Good
const minimumAge = 18
const millisecondsPerDay = 24 * 60 * 60 * 1000
const maxItemsPerPage = 100

if user.Age < minimumAge { }
time.AfterFunc(time.Duration(millisecondsPerDay)*time.Millisecond, callback)
if len(items) > maxItemsPerPage { }
```

### 3. Long Parameter Lists

**Problem:** Functions with too many parameters are hard to use and maintain.

```go
// Bad
func createUser(
    firstName, lastName, email, password string,
    age int,
    address, phone, role string,
) (*User, error) {
    // ...
    return nil, nil
}
```

**Solution:** Use structs or parameter objects.

```go
// Good
type UserData struct {
    FirstName string
    LastName  string
    Email     string
    Password  string
    Age       int
    Address   string
    Phone     string
    Role      string
}

func createUser(userData UserData) (*User, error) {
    // ...
    return nil, nil
}
```

### 4. Feature Envy

**Problem:** A method uses more features of another class than its own.

```go
// Bad
type Order struct {
    items []OrderItem
}

func (o *Order) CalculateTotal() float64 {
    total := 0.0
    for _, item := range o.items {
        total += item.Price * float64(item.Quantity) // Envies OrderItem
    }
    return total
}
```

**Solution:** Move the method to the struct it envies.

```go
// Good
type Order struct {
    items []OrderItem
}

func (o *Order) CalculateTotal() float64 {
    total := 0.0
    for _, item := range o.items {
        total += item.GetSubtotal()
    }
    return total
}

type OrderItem struct {
    Price    float64
    Quantity int
}

func (i *OrderItem) GetSubtotal() float64 {
    return i.Price * float64(i.Quantity)
}
```

### 5. Dead Code

**Problem:** Unused code that clutters the codebase.

```go
// Bad
func oldRequestModule(url string) error {
    // This function is never called
    return nil
}

func requestModule(url string) error {
    // Active code
    return nil
}
```

**Solution:** Remove unused code.

```go
// Good
func requestModule(url string) error {
    // Only keep what's used
    return nil
}
```

### 6. Comments Explaining What Code Does

**Problem:** Comments that restate what the code already says.

```go
// Bad
// This function calculates the total
func calculateTotal(items []Item) float64 {
    // Initialize total to zero
    total := 0.0
    // Loop through items
    for _, item := range items {
        // Add item price to total
        total += item.Price
    }
    // Return the total
    return total
}
```

**Solution:** Write self-documenting code.

```go
// Good
func calculateTotal(items []Item) float64 {
    total := 0.0
    for _, item := range items {
        total += item.Price
    }
    return total
}
```

## Complete Example: Applying All Principles

Here's a comprehensive example that demonstrates all the principles working together:

### Initial Bad Code

```go
type OrderProcessor struct{}

func (p *OrderProcessor) Process(order interface{}) error {
    orderMap := order.(map[string]interface{})
    items, ok := orderMap["items"].([]interface{})
    if !ok || len(items) == 0 {
        return fmt.Errorf("invalid order")
    }
    total := 0.0
    for i := 0; i < len(items); i++ {
        item := items[i].(map[string]interface{})
        price, priceOk := item["price"].(float64)
        quantity, qtyOk := item["quantity"].(float64)
        if priceOk && qtyOk {
            total += price * quantity
        }
    }
    tax := total * 0.1
    finalTotal := total + tax
    customer, custOk := orderMap["customer"].(map[string]interface{})
    if !custOk {
        return fmt.Errorf("invalid customer")
    }
    creditLimit, limitOk := customer["creditLimit"].(float64)
    if !limitOk {
        return fmt.Errorf("invalid customer")
    }
    if creditLimit >= finalTotal {
        customer["creditLimit"] = creditLimit - finalTotal
        orderMap["total"] = finalTotal
        orderMap["status"] = "processed"
        db := &MySQLDatabase{}
        db.Save(order)
        email := &EmailService{}
        email.Send(customer["email"].(string), "Order processed", fmt.Sprintf("Total: %.2f", finalTotal))
    } else {
        return fmt.Errorf("insufficient credit")
    }
    return nil
}
```

### Refactored Good Code

```go
// SOLID: Single Responsibility - Each struct/interface has one job
type OrderRepository interface {
    Save(ctx context.Context, order *Order) error
}

type EmailService interface {
    Send(ctx context.Context, to, subject, body string) error
}

type TaxCalculator interface {
    Calculate(subtotal float64) float64
}

// SOLID: Dependency Inversion - Depend on abstractions
type OrderProcessor struct {
    orderRepository OrderRepository
    emailService    EmailService
    taxCalculator   TaxCalculator
}

func NewOrderProcessor(
    orderRepo OrderRepository,
    email EmailService,
    taxCalc TaxCalculator,
) *OrderProcessor {
    return &OrderProcessor{
        orderRepository: orderRepo,
        emailService:    email,
        taxCalculator:   taxCalc,
    }
}

// Small function, single level of abstraction
func (p *OrderProcessor) Process(ctx context.Context, order *Order) error {
    if err := p.validateOrder(order); err != nil {
        return err
    }
    total, err := p.calculateTotal(order)
    if err != nil {
        return err
    }
    if err := p.validateCreditLimit(order.Customer, total); err != nil {
        return err
    }
    return p.completeOrder(ctx, order, total)
}

// Early returns for validation
func (p *OrderProcessor) validateOrder(order *Order) error {
    if order == nil {
        return fmt.Errorf("order is required")
    }
    if len(order.Items) == 0 {
        return fmt.Errorf("order must have items")
    }
    return nil
}

// Small, focused function
func (p *OrderProcessor) calculateTotal(order *Order) (float64, error) {
    subtotal, err := p.calculateSubtotal(order.Items)
    if err != nil {
        return 0, err
    }
    tax := p.taxCalculator.Calculate(subtotal)
    return subtotal + tax, nil
}

// DRY - Reusable calculation
func (p *OrderProcessor) calculateSubtotal(items []OrderItem) (float64, error) {
    subtotal := 0.0
    for _, item := range items {
        if err := p.validateItem(item); err != nil {
            return 0, err
        }
        subtotal += item.GetSubtotal()
    }
    return subtotal, nil
}

// Early return
func (p *OrderProcessor) validateItem(item OrderItem) error {
    if item.Price <= 0 {
        return fmt.Errorf("invalid item price")
    }
    if item.Quantity <= 0 {
        return fmt.Errorf("invalid item quantity")
    }
    return nil
}

// Early return
func (p *OrderProcessor) validateCreditLimit(customer *Customer, total float64) error {
    if customer == nil {
        return fmt.Errorf("customer is required")
    }
    if customer.CreditLimit < total {
        return fmt.Errorf("insufficient credit limit")
    }
    return nil
}

// Single level of abstraction
func (p *OrderProcessor) completeOrder(ctx context.Context, order *Order, total float64) error {
    p.applyOrderToCustomer(order.Customer, total)
    p.finalizeOrder(order, total)
    if err := p.persistOrder(ctx, order); err != nil {
        return err
    }
    return p.notifyCustomer(ctx, order, total)
}

// TDA - Tell, don't ask
func (p *OrderProcessor) applyOrderToCustomer(customer *Customer, amount float64) {
    customer.DeductCredit(amount)
}

// Small function
func (p *OrderProcessor) finalizeOrder(order *Order, total float64) {
    order.Total = total
    order.Status = "processed"
    order.ProcessedAt = time.Now()
}

// Single responsibility
func (p *OrderProcessor) persistOrder(ctx context.Context, order *Order) error {
    return p.orderRepository.Save(ctx, order)
}

// Single responsibility
func (p *OrderProcessor) notifyCustomer(ctx context.Context, order *Order, total float64) error {
    return p.emailService.Send(
        ctx,
        order.Customer.GetEmail(),
        "Order Processed",
        fmt.Sprintf("Your order total is $%.2f", total),
    )
}

// SOLID: Tell, Don't Ask - Encapsulate behavior
type Customer struct {
    email       string
    creditLimit float64
}

func NewCustomer(email string, creditLimit float64) *Customer {
    return &Customer{
        email:       email,
        creditLimit: creditLimit,
    }
}

func (c *Customer) DeductCredit(amount float64) error {
    if c.creditLimit < amount {
        return fmt.Errorf("insufficient credit")
    }
    c.creditLimit -= amount
    return nil
}

func (c *Customer) GetEmail() string {
    return c.email
}

// SOLID: Tell, Don't Ask
type OrderItem struct {
    Price    float64
    Quantity int
}

func (i *OrderItem) GetSubtotal() float64 {
    return i.Price * float64(i.Quantity)
}

// Meaningful names, KISS
type StandardTaxCalculator struct {
    taxRate float64
}

func NewStandardTaxCalculator() *StandardTaxCalculator {
    return &StandardTaxCalculator{taxRate: 0.1}
}

func (c *StandardTaxCalculator) Calculate(subtotal float64) float64 {
    return subtotal * c.taxRate
}

// Usage - Dependency Injection
func main() {
    ctx := context.Background()
    orderRepository := &MySQLOrderRepository{}
    emailService := &SMTPEmailService{}
    taxCalculator := NewStandardTaxCalculator()
    
    processor := NewOrderProcessor(
        orderRepository,
        emailService,
        taxCalculator,
    )
    
    order := &Order{} // Initialize order
    _ = processor.Process(ctx, order)
}
```

## Summary

**Always apply these principles:**

1. ✅ **SOLID** - Single Responsibility, Open/Closed, Liskov Substitution, Interface Segregation, Dependency Inversion
2. ✅ **Clean Code** - KISS, YAGNI, DRY, TDA
3. ✅ **Small Functions** - Do one thing, fit on screen
4. ✅ **Meaningful Names** - Intention-revealing, pronounceable, searchable
5. ✅ **Single Level of Abstraction** - High-level calls low-level
6. ✅ **Early Returns** - Guard clauses, reduce nesting
7. ✅ **Avoid Anti-Patterns** - God objects, magic numbers, long parameter lists, feature envy, dead code

**Remember:** Code is read far more often than it's written. Write code for humans, not just for computers.

---

## ⚠️ Critical Reminder

**ALWAYS follow the `<critical_validations>` section at the top of this document. These rules are mandatory and must be enforced in all code.**

Failure to follow these critical validations will result in non-compliant code that must be refactored.
