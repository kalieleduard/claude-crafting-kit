---
name: code-standards
description: Expert in code formatting and style standards including function length limits, parameter count limits, no line breaks in functions, no comments (use clarifying names), and no magic numbers. **ALWAYS use when writing or reviewing code to ensure consistent formatting and style.** Use proactively to enforce code readability and maintainability standards. Examples - "format code", "review function", "check code style", "refactor function", "improve readability", "apply code standards".
---

<critical_validations>
- No Line Breaks in Functions: Functions must be written without blank lines between statements within the function body; keep all function logic on consecutive lines.
- No Comments: Never use comments to explain what code does; use clarifying, intention-revealing names instead; code should be self-documenting.
- Function Length: Functions must not exceed 30 lines; if a function exceeds this limit, break it into smaller functions.
- Function Parameters: Functions should have few parameters (ideally 3 or fewer); use structs or parameter objects for functions requiring more data.
- No Magic Numbers: Never use literal numbers in code without named constants; all numeric values must be defined as named constants with clear, descriptive names.
</critical_validations>

<related_skills>

→ Use `code-quality` skill for SOLID principles, Clean Code patterns, and design standards when applying these formatting standards to code design

→ Use `golang-engineer` skill for Go-specific implementation patterns when applying these standards to Go code

</related_skills>

<no_line_breaks>

**Rule:** Functions must be written without blank lines between statements within the function body. Keep all function logic on consecutive lines without line breaks separating statements.

**Why it matters:** Consistent formatting improves readability and reduces visual clutter. Line breaks within functions can make code appear fragmented and harder to scan.

### ✅ Correct: No Line Breaks Inside Function

```go
func (s *Service) CreateUser(ctx context.Context, email, name string) (*User, error) {
    user := &User{Email: email, Name: name}
    if err := user.Validate(); err != nil {
        return nil, err
    }
    if err := s.repo.Save(ctx, user); err != nil {
        return nil, err
    }
    return user, nil
}
```

### ❌ Wrong: Line Breaks Inside Function

```go
func (s *Service) CreateUser(ctx context.Context, email, name string) (*User, error) {
    user := &User{Email: email, Name: name}

    if err := user.Validate(); err != nil {
        return nil, err
    }

    if err := s.repo.Save(ctx, user); err != nil {
        return nil, err
    }

    return user, nil
}
```

### Exception: Logical Grouping

While blank lines within functions are generally prohibited, you may use a single blank line to separate distinct logical sections when absolutely necessary for readability, but this should be rare and only for very long functions (approaching the 30-line limit).

</no_line_breaks>

<no_comments>

**Rule:** Never use comments to explain what code does. Instead, use clarifying, intention-revealing names that make the code self-documenting. Code should read like well-written prose.

**Why it matters:** Comments become outdated and can mislead. Self-documenting code with clear names is always up-to-date and easier to understand. Comments that explain "what" are code smells indicating the code needs better naming.

### ❌ Wrong: Comments Explaining Code

```go
// This function calculates the total price
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

// Process user data
func processUser(user *User) error {
    // Validate user
    if user.Email == "" {
        return fmt.Errorf("email required")
    }
    // Save to database
    return db.Save(user)
}
```

### ✅ Correct: Self-Documenting Code

```go
func calculateTotalPrice(items []Item) float64 {
    total := 0.0
    for _, item := range items {
        total += item.Price
    }
    return total
}

func saveValidatedUser(user *User) error {
    if err := validateUserEmail(user); err != nil {
        return err
    }
    return persistUserToDatabase(user)
}

func validateUserEmail(user *User) error {
    if user.Email == "" {
        return fmt.Errorf("email required")
    }
    return nil
}

func persistUserToDatabase(user *User) error {
    return db.Save(user)
}
```

### When Comments Are Acceptable

Comments are acceptable only for:
1. **Public API documentation** (package-level comments, exported function documentation)
2. **Explaining "why" not "what"** - when business logic or decisions need context
3. **Temporary workarounds** - marking known issues or technical debt

```go
// ✅ Acceptable: Public API documentation
// CreateUser creates a new user account with the provided email and name.
// Returns an error if the email is already registered.
func CreateUser(ctx context.Context, email, name string) (*User, error) {
    // Implementation
}

// ✅ Acceptable: Explaining "why"
// Using legacy API endpoint due to compatibility requirements with older clients.
// TODO: Migrate to v2 API when all clients are updated.
func fetchUserData(userID string) (*User, error) {
    return legacyAPI.GetUser(userID)
}
```

</no_comments>

<function_length>

**Rule:** Functions must not exceed 30 lines. If a function exceeds this limit, break it into smaller, focused functions.

**Why it matters:** Small functions are easier to understand, test, and maintain. Functions that fit on a screen (approximately 30 lines) can be comprehended at a glance without scrolling.

### ❌ Wrong: Function Exceeding 30 Lines

```go
func processOrder(order *Order) error {
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
    if err := database.Save(order); err != nil {
        return err
    }
    emailService.Send(order.Customer.Email, "Order processed", fmt.Sprintf("Your order total is %.2f", finalTotal))
    inventoryService.Update(order.Items)
    analyticsService.Track("order_processed", map[string]interface{}{
        "orderId": order.ID,
        "total":   finalTotal,
    })
    return nil
}
```

### ✅ Correct: Functions Under 30 Lines

```go
func processOrder(order *Order) error {
    if err := validateOrder(order); err != nil {
        return err
    }
    total, err := calculateOrderTotal(order.Items)
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
    trackOrderAnalytics(order)
    return nil
}

func validateOrder(order *Order) error {
    if len(order.Items) == 0 {
        return fmt.Errorf("order must have items")
    }
    return nil
}

func calculateOrderTotal(items []OrderItem) (float64, error) {
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
    const taxRate = 0.1
    return total * taxRate
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

func trackOrderAnalytics(order *Order) {
    analyticsService.Track("order_processed", map[string]interface{}{
        "orderId": order.ID,
        "total":   order.Total,
    })
}
```

</function_length>

<function_parameters>

**Rule:** Functions should have few parameters (ideally 3 or fewer). Use structs or parameter objects for functions requiring more data.

**Why it matters:** Functions with many parameters are hard to understand, use, and maintain. They increase cognitive load and make it easy to pass arguments in the wrong order. Structs provide better readability and extensibility.

### ❌ Wrong: Too Many Parameters

```go
func createUser(
    firstName, lastName, email, password string,
    age int,
    address, phone, role string,
    isActive bool,
    createdAt time.Time,
) (*User, error) {
    // Implementation
}

func sendEmail(
    to, from, subject, body string,
    isHTML bool,
    attachments []string,
    priority int,
) error {
    // Implementation
}
```

### ✅ Correct: Few Parameters or Struct

```go
type UserData struct {
    FirstName string
    LastName  string
    Email     string
    Password  string
    Age       int
    Address   string
    Phone     string
    Role      string
    IsActive  bool
    CreatedAt time.Time
}

func createUser(userData UserData) (*User, error) {
    // Implementation
}

type EmailRequest struct {
    To          string
    From        string
    Subject     string
    Body        string
    IsHTML      bool
    Attachments []string
    Priority    int
}

func sendEmail(request EmailRequest) error {
    // Implementation
}
```

### ✅ Correct: Functions with 3 or Fewer Parameters

```go
func calculateTotal(price float64, quantity int, discount float64) float64 {
    subtotal := price * float64(quantity)
    return subtotal * (1 - discount)
}

func findUserByEmail(ctx context.Context, email string) (*User, error) {
    // Implementation
}

func updateUserStatus(ctx context.Context, userID string, isActive bool) error {
    // Implementation
}
```

### Guidelines

1. **0-1 parameters:** Ideal for simple operations
2. **2-3 parameters:** Acceptable for most functions
3. **4+ parameters:** Use a struct or parameter object
4. **Context parameter:** `context.Context` doesn't count toward the limit (it's a standard Go pattern)

</function_parameters>

<no_magic_numbers>

**Rule:** Never use literal numbers in code without named constants. All numeric values must be defined as named constants with clear, descriptive names that explain their purpose.

**Why it matters:** Magic numbers make code hard to understand and maintain. Named constants make the code self-documenting and easier to modify. They also prevent errors from using incorrect values.

### ❌ Wrong: Magic Numbers

```go
func validateUser(user *User) error {
    if user.Age < 18 {
        return fmt.Errorf("user must be 18 or older")
    }
    if len(user.Password) < 8 {
        return fmt.Errorf("password must be at least 8 characters")
    }
    if len(user.Username) > 50 {
        return fmt.Errorf("username must be 50 characters or less")
    }
    return nil
}

func calculateDiscount(price float64, isMember bool) float64 {
    if isMember {
        return price * 0.1
    }
    return price * 0.05
}

func scheduleTask() {
    time.AfterFunc(86400000*time.Millisecond, callback)
}

func paginateResults(items []Item, page int) []Item {
    start := (page - 1) * 10
    end := start + 10
    if end > len(items) {
        end = len(items)
    }
    return items[start:end]
}
```

### ✅ Correct: Named Constants

```go
const (
    minimumAge          = 18
    minimumPasswordLength = 8
    maximumUsernameLength = 50
    memberDiscountRate    = 0.1
    regularDiscountRate   = 0.05
    millisecondsPerDay    = 24 * 60 * 60 * 1000
    itemsPerPage         = 10
)

func validateUser(user *User) error {
    if user.Age < minimumAge {
        return fmt.Errorf("user must be %d or older", minimumAge)
    }
    if len(user.Password) < minimumPasswordLength {
        return fmt.Errorf("password must be at least %d characters", minimumPasswordLength)
    }
    if len(user.Username) > maximumUsernameLength {
        return fmt.Errorf("username must be %d characters or less", maximumUsernameLength)
    }
    return nil
}

func calculateDiscount(price float64, isMember bool) float64 {
    discountRate := regularDiscountRate
    if isMember {
        discountRate = memberDiscountRate
    }
    return price * discountRate
}

func scheduleTask() {
    time.AfterFunc(time.Duration(millisecondsPerDay)*time.Millisecond, callback)
}

func paginateResults(items []Item, page int) []Item {
    start := (page - 1) * itemsPerPage
    end := start + itemsPerPage
    if end > len(items) {
        end = len(items)
    }
    return items[start:end]
}
```

### Common Magic Numbers to Avoid

1. **Ages and limits:** `18`, `21`, `65`
2. **Time durations:** `86400000` (milliseconds per day), `3600` (seconds per hour)
3. **Sizes and limits:** `1024`, `2048`, `4096` (memory/file sizes)
4. **Percentages and rates:** `0.1`, `0.2`, `0.05`
5. **Array indices:** `0`, `1` (when they represent specific positions)
6. **HTTP status codes:** `200`, `404`, `500` (use constants from `net/http`)
7. **Pagination:** `10`, `20`, `50` (items per page)

### Exception: Obvious Values

Some values are so obvious they don't need constants:
- `0` for initialization or zero values
- `1` for incrementing or single items
- `-1` for "not found" or error cases
- Mathematical constants like `math.Pi`

```go
// ✅ Acceptable: Obvious values
for i := 0; i < len(items); i++ {
    processItem(items[i])
}

if count == 0 {
    return nil
}

// ❌ Still wrong: Should use constant
if user.Age < 18 {
    return fmt.Errorf("too young")
}
```

</no_magic_numbers>

<complete_example>

Here's a comprehensive example demonstrating all code standards working together:

### ❌ Bad Code: Violates All Standards

```go
func processOrder(order *Order) error {
    // Validate order
    if len(order.Items) == 0 {
        return fmt.Errorf("order must have items")
    }

    // Calculate total
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

    // Check credit limit
    if order.Customer.CreditLimit < total {
        return fmt.Errorf("credit limit exceeded")
    }

    // Apply discount
    discount := 0.0
    if order.Customer.IsMember {
        discount = total * 0.1
    } else {
        discount = total * 0.05
    }

    // Calculate final total
    tax := (total - discount) * 0.1
    finalTotal := total - discount + tax

    // Update customer
    order.Customer.CreditLimit -= finalTotal

    // Update order
    order.Total = finalTotal
    order.Status = "processed"
    order.ProcessedAt = time.Now()

    // Save to database
    if err := database.Save(order); err != nil {
        return err
    }

    // Send email
    emailService.Send(
        order.Customer.Email,
        "Order processed",
        fmt.Sprintf("Your order total is %.2f", finalTotal),
    )

    // Update inventory
    inventoryService.Update(order.Items)

    // Track analytics
    analyticsService.Track("order_processed", map[string]interface{}{
        "orderId": order.ID,
        "total":   finalTotal,
    })

    return nil
}
```

### ✅ Good Code: Follows All Standards

```go
const (
    minimumOrderItems      = 1
    minimumItemQuantity    = 1
    minimumItemPrice       = 0.0
    memberDiscountRate     = 0.1
    regularDiscountRate    = 0.05
    taxRate                = 0.1
    orderStatusProcessed   = "processed"
)

func processOrder(order *Order) error {
    if err := validateOrder(order); err != nil {
        return err
    }
    subtotal, err := calculateSubtotal(order.Items)
    if err != nil {
        return err
    }
    discount := calculateDiscount(subtotal, order.Customer.IsMember)
    total := calculateFinalTotal(subtotal, discount)
    if err := validateCreditLimit(order.Customer, total); err != nil {
        return err
    }
    applyOrderToCustomer(order.Customer, total)
    finalizeOrder(order, total)
    if err := persistOrder(order); err != nil {
        return err
    }
    notifyCustomer(order, total)
    updateInventory(order.Items)
    trackOrderAnalytics(order, total)
    return nil
}

func validateOrder(order *Order) error {
    if len(order.Items) < minimumOrderItems {
        return fmt.Errorf("order must have at least %d item", minimumOrderItems)
    }
    return nil
}

func calculateSubtotal(items []OrderItem) (float64, error) {
    subtotal := 0.0
    for _, item := range items {
        if err := validateItem(item); err != nil {
            return 0, err
        }
        subtotal += item.Price * float64(item.Quantity)
    }
    return subtotal, nil
}

func validateItem(item OrderItem) error {
    if item.Quantity < minimumItemQuantity {
        return fmt.Errorf("item quantity must be at least %d", minimumItemQuantity)
    }
    if item.Price < minimumItemPrice {
        return fmt.Errorf("item price must be at least %.2f", minimumItemPrice)
    }
    return nil
}

func calculateDiscount(subtotal float64, isMember bool) float64 {
    discountRate := regularDiscountRate
    if isMember {
        discountRate = memberDiscountRate
    }
    return subtotal * discountRate
}

func calculateFinalTotal(subtotal, discount float64) float64 {
    discountedSubtotal := subtotal - discount
    tax := discountedSubtotal * taxRate
    return discountedSubtotal + tax
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
    order.Total = total
    order.Status = orderStatusProcessed
    order.ProcessedAt = time.Now()
}

func persistOrder(order *Order) error {
    return database.Save(order)
}

func notifyCustomer(order *Order, total float64) {
    emailService.Send(
        order.Customer.Email,
        "Order processed",
        fmt.Sprintf("Your order total is %.2f", total),
    )
}

func updateInventory(items []OrderItem) {
    inventoryService.Update(items)
}

func trackOrderAnalytics(order *Order, total float64) {
    analyticsService.Track("order_processed", map[string]interface{}{
        "orderId": order.ID,
        "total":   total,
    })
}
```

</complete_example>

<summary>
**Always enforce these code standards:**

1. ✅ **No Line Breaks in Functions** - Keep all function logic on consecutive lines
2. ✅ **No Comments** - Use clarifying names instead; code should be self-documenting
3. ✅ **Function Length** - Maximum 30 lines per function
4. ✅ **Function Parameters** - Few parameters (ideally 3 or fewer); use structs for more
5. ✅ **No Magic Numbers** - All numeric values must be named constants

**Remember:** Consistent code standards improve readability, maintainability, and reduce cognitive load. Code that follows these standards is easier to understand, test, and modify.

</summary>

## ⚠️ Critical Reminder

**ALWAYS follow the `<critical_validations>` section at the top of this document. These code formatting and style rules are mandatory and must be enforced in all code.**

Failure to follow these critical validations will result in non-compliant code that must be refactored.

