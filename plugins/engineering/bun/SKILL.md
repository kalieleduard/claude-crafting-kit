---
name: bun
description: Expert in Bun.js runtime, bundler, test runner, and package manager. Specializes in high-performance JavaScript/TypeScript applications, native APIs, SQLite integration, HTTP servers, and Bun-specific optimizations. **ALWAYS use when building Bun.js applications, optimizing performance, working with native Bun APIs, or leveraging Bun's built-in features.** Use proactively to ensure proper use of Bun's capabilities, performance best practices, and native integrations. Examples - "create Bun server", "optimize Bun performance", "use Bun SQLite", "setup Bun project", "write Bun tests", "bundle with Bun".
---

<critical_validations>
- Native APIs: Prefer Bun's native APIs (bun:sqlite, Bun.serve, Bun.file) over Node.js polyfills when available; leverage Bun's performance advantages.
- SQLite Integration: Use bun:sqlite for database operations; enable WAL mode for better concurrency; use transactions for atomic operations; close connections properly with 'using' statements.
- HTTP Server: Use Bun.serve() with routes object for better performance; leverage native streaming with async generators; use proper error handling.
- File Operations: Use Bun.file() and Bun.write() for efficient file I/O; prefer streaming for large files.
- Performance: Leverage Bun's fast startup and execution; use native implementations over polyfills; optimize database queries with prepared statements.
- TypeScript Configuration: Configure tsconfig.json properly for Bun with module: "Preserve", moduleResolution: "bundler", and allowImportingTsExtensions: true.
</critical_validations>

<related_skills>

→ Use `backend` skill for Clean Architecture, layered design, and project structure when building backend services with Bun

→ Use `code-quality` skill for SOLID principles, Clean Code patterns, and design standards when designing Bun applications

→ Use `code-standards` skill for code formatting and style standards when writing Bun code

</related_skills>

<http_server>

### Bun.serve() Best Practices

Bun provides a high-performance HTTP server through `Bun.serve()`. Use the `routes` object for better performance and cleaner code organization.

#### Basic Server Setup

```typescript
const server = Bun.serve({
  port: 3000,
  routes: {
    "/": new Response("Hello from Bun!"),
    "/api/status": Response.json({ status: "ok" }),
  },
  fetch(req) {
    return new Response("Not Found", { status: 404 });
  },
});

console.log(`Server running at ${server.url}`);
```

#### Route Organization

Use the `routes` object (Bun v1.2.3+) for static and dynamic routes:

```typescript
const server = Bun.serve({
  routes: {
    // Static routes
    "/api/status": new Response("OK"),
    
    // Dynamic routes with parameters
    "/users/:id": (req) => {
      return new Response(`User ${req.params.id}`);
    },
    
    // Per-HTTP method handlers
    "/api/posts": {
      GET: () => Response.json({ posts: [] }),
      POST: async (req) => {
        const body = await req.json();
        return Response.json({ created: true, ...body }, { status: 201 });
      },
    },
    
    // Wildcard routes
    "/api/*": Response.json({ message: "Not found" }, { status: 404 }),
  },
});
```

#### Streaming Responses

Use async generators for streaming large responses:

```typescript
Bun.serve({
  port: 3000,
  fetch(req) {
    return new Response(
      async function* () {
        yield "Hello, ";
        await Bun.sleep(100);
        yield "world!";
        yield new Uint8Array(["\n".charCodeAt(0)]);
      },
      { headers: { "Content-Type": "text/plain" } }
    );
  },
});
```

#### Error Handling

Always provide error handling:

```typescript
Bun.serve({
  routes: {
    "/api/data": async (req) => {
      try {
        const data = await fetchData();
        return Response.json(data);
      } catch (error) {
        console.error(error);
        return Response.json({ error: "Internal Server Error" }, { status: 500 });
      }
    },
  },
  error(error) {
    console.error(error);
    return new Response("Internal Server Error", { status: 500 });
  },
});
```

#### Static File Serving

Use `Bun.file()` for efficient file serving:

```typescript
Bun.serve({
  routes: {
    "/favicon.ico": async () => {
      const file = Bun.file("./favicon.ico");
      return new Response(file, {
        headers: { "Content-Type": "image/x-icon" },
      });
    },
  },
});
```

#### Server Management

Use server methods for lifecycle management:

```typescript
const server = Bun.serve({ /* ... */ });

// Stop server gracefully
await server.stop();

// Reload handlers without restarting
server.reload({ /* new options */ });

// Get server metrics
console.log(`Pending requests: ${server.pendingRequests}`);
console.log(`Active WebSockets: ${server.pendingWebSockets}`);
```

</http_server>

<sqlite>

### SQLite Best Practices with bun:sqlite

Bun provides native SQLite support through `bun:sqlite`. Use it for fast, embedded database operations.

#### Database Initialization

```typescript
import { Database } from "bun:sqlite";

// File-based database
const db = new Database("app.db");

// In-memory database
const inMemoryDb = new Database(":memory:");

// Read-only database
const readOnlyDb = new Database("app.db", { readonly: true });
```

#### Enable WAL Mode

Enable Write-Ahead Logging for better concurrency:

```typescript
const db = new Database("app.db");
db.exec("PRAGMA journal_mode = WAL;");
```

#### Prepared Statements

Always use prepared statements for better performance and security:

```typescript
const db = new Database("app.db");

// Prepare once, use many times
const insertUser = db.prepare("INSERT INTO users (name, email) VALUES (?, ?)");
const getUser = db.prepare("SELECT * FROM users WHERE id = ?");

// Execute with parameters
insertUser.run("John Doe", "john@example.com");
const user = getUser.get(1);
```

#### Transactions

Use transactions for atomic operations:

```typescript
const insert = db.prepare("INSERT INTO cats (name) VALUES ($name)");
const insertCats = db.transaction((cats) => {
  for (const cat of cats) {
    insert.run(cat);
  }
  return cats.length;
});

const count = insertCats([
  { $name: "Keanu" },
  { $name: "Salem" },
  { $name: "Crookshanks" },
]);
```

#### Resource Management

Use `using` statements for automatic resource cleanup:

```typescript
import { Database } from "bun:sqlite";

{
  using db = new Database("mydb.sqlite");
  using query = db.query("SELECT * FROM users WHERE id = ?");
  const user = query.get(1);
  // Database and query automatically closed when block exits
}
```

#### Query Methods

Use appropriate query methods:

```typescript
const db = new Database("app.db");
const query = db.query("SELECT * FROM users WHERE age > ?");

// Get single row
const user = query.get(18);

// Get all rows
const users = query.all(18);

// Get first column of first row
const count = db.query("SELECT COUNT(*) FROM users").get();
```

#### Database Import

Import SQLite databases directly:

```typescript
// External database (default)
import db from "./my.db" with { type: "sqlite" };

// Embedded database (included in bundle)
import db from "./my.db" with { type: "sqlite", embed: "true" };
```

#### Serialization

Serialize and deserialize databases:

```typescript
const oldDb = new Database("mydb.sqlite");
const contents = oldDb.serialize(); // => Uint8Array
const newDb = Database.deserialize(contents);
```

#### Strict Mode

Enable strict mode for better type safety:

```typescript
const db = new Database(":memory:", { strict: true });

// With strict mode, parameters don't need $ prefix
const query = db.query("SELECT * FROM users WHERE id = $id");
query.all({ id: 1 }); // Works with strict: true
```

</sqlite>

<file_operations>

### File Operations Best Practices

Bun provides efficient file I/O through `Bun.file()` and `Bun.write()`.

#### Reading Files

```typescript
// Read file as text
const file = Bun.file("./data.txt");
const text = await file.text();

// Read file as bytes
const bytes = await file.bytes();

// Read file as JSON
const json = await file.json();

// Read file as stream
const stream = file.stream();
```

#### Writing Files

```typescript
// Write text
await Bun.write("./output.txt", "Hello, Bun!");

// Write JSON
await Bun.write("./data.json", JSON.stringify({ key: "value" }));

// Write from Response
const response = await fetch("https://example.com/file.txt");
await Bun.write("./downloaded.txt", response);

// Write bytes
await Bun.write("./binary.bin", new Uint8Array([1, 2, 3]));
```

#### File Serving

Serve files efficiently:

```typescript
Bun.serve({
  async fetch(req) {
    const path = new URL(req.url).pathname;
    const file = Bun.file(`./public${path}`);
    
    if (await file.exists()) {
      return new Response(file);
    }
    
    return new Response("Not Found", { status: 404 });
  },
});
```

#### File Metadata

Check file properties:

```typescript
const file = Bun.file("./data.txt");

console.log(await file.exists());
console.log(file.size);
console.log(file.type);
console.log(file.name);
```

</file_operations>

<performance>

### Performance Optimization

Bun is designed for speed. Follow these practices to maximize performance.

#### Use Native APIs

Prefer Bun's native APIs over Node.js polyfills:

```typescript
// ✅ Good: Use Bun.serve()
Bun.serve({ /* ... */ });

// ❌ Avoid: Using Node.js http module
import http from "node:http";
```

#### Database Preconnection

Preconnect to databases at startup:

```bash
bun --sql-preconnect index.js
```

#### Prepared Statements

Always use prepared statements for repeated queries:

```typescript
// ✅ Good: Prepare once, execute many times
const query = db.prepare("SELECT * FROM users WHERE id = ?");
const user1 = query.get(1);
const user2 = query.get(2);

// ❌ Avoid: Preparing on every call
const user1 = db.query("SELECT * FROM users WHERE id = ?").get(1);
const user2 = db.query("SELECT * FROM users WHERE id = ?").get(2);
```

#### Streaming

Use streaming for large data:

```typescript
// ✅ Good: Stream response
Bun.serve({
  fetch(req) {
    return new Response(async function* () {
      for (let i = 0; i < 1000; i++) {
        yield `Line ${i}\n`;
      }
    });
  },
});

// ❌ Avoid: Loading everything into memory
Bun.serve({
  fetch(req) {
    const data = generateLargeData();
    return new Response(data);
  },
});
```

#### WAL Mode for SQLite

Enable WAL mode for better concurrent performance:

```typescript
const db = new Database("app.db");
db.exec("PRAGMA journal_mode = WAL;");
```

#### Hot Reloading

Use `--hot` flag for development:

```bash
bun --hot index.ts
```

This enables hot module reloading without restarting the server.

</performance>

<testing>

### Testing Best Practices

Bun includes a fast test runner built-in. Use it for all testing needs.

#### Basic Test Structure

```typescript
import { test, expect, describe } from "bun:test";

describe("User Service", () => {
  test("should create user", () => {
    const user = createUser("John", "john@example.com");
    expect(user.name).toBe("John");
    expect(user.email).toBe("john@example.com");
  });
});
```

#### Test Hooks

Keep hooks simple and focused:

```typescript
// ✅ Good: Simple setup
beforeEach(() => {
  clearLocalStorage();
  resetMocks();
});

// ❌ Avoid: Complex logic in hooks
beforeEach(async () => {
  const data = await fetchComplexData();
  await processData(data);
  await setupMultipleServices(data);
});
```

#### Snapshot Testing

Use snapshots for stable outputs:

```typescript
test("formats currency correctly", () => {
  const formatted = formatCurrency(99.99);
  expect(formatted).toMatchInlineSnapshot(`"$99.99"`);
});
```

Normalize dynamic data in snapshots:

```typescript
test("API response format", () => {
  const response = {
    data: { id: 1, name: "Test" },
    timestamp: Date.now(),
    requestId: generateId(),
  };
  expect({
    ...response,
    timestamp: "TIMESTAMP",
    requestId: "REQUEST_ID",
  }).toMatchSnapshot();
});
```

#### Coverage Configuration

Configure coverage exclusions in `bunfig.toml`:

```toml
[test]
coveragePathIgnorePatterns = [
  "**/*.spec.ts",
  "**/*.test.ts",
  "src/utils/**",
  "*.config.js",
  "dist/**",
  "build/**",
]
```

</testing>

<typescript_config>

### TypeScript Configuration

Configure TypeScript properly for Bun to leverage its features.

#### Recommended tsconfig.json

```json
{
  "compilerOptions": {
    "lib": ["ESNext"],
    "target": "ESNext",
    "module": "Preserve",
    "moduleDetection": "force",
    "moduleResolution": "bundler",
    "allowImportingTsExtensions": true,
    "verbatimModuleSyntax": true,
    "noEmit": true
  }
}
```

#### Key Settings

- **module: "Preserve"**: Preserves import/export statements
- **moduleResolution: "bundler"**: Uses bundler-style resolution
- **allowImportingTsExtensions**: Allows importing `.ts` files directly
- **verbatimModuleSyntax**: Preserves module syntax exactly
- **noEmit**: Bun handles compilation, no need to emit

</typescript_config>

<bundling>

### Bundling Best Practices

Bun includes a fast bundler. Use it for building applications.

#### Bundle Executables

Create standalone executables:

```bash
bun build ./index.ts --outdir ./dist --target bun
```

#### Embed Resources

Embed SQLite databases and other resources:

```typescript
import db from "./my.db" with { type: "sqlite", embed: "true" };
```

#### Loaders

Use appropriate loaders for different file types:

```typescript
// SQLite database
import db from "./data.db" with { type: "sqlite" };

// HTML files
import html from "./index.html";

// Text files
import text from "./data.txt" with { type: "text" };
```

</bundling>

<package_management>

### Package Management

Bun includes a fast package manager. Use it instead of npm/yarn.

#### Install Dependencies

```bash
# Install package
bun add <package>

# Install dev dependency
bun add -d <package>

# Install from package.json
bun install
```

#### Run Scripts

```bash
# Run script from package.json
bun run <script>

# Run TypeScript file directly
bun run index.ts

# Run with hot reload
bun --hot index.ts
```

#### Performance

Bun's package manager is significantly faster than npm/yarn:
- Faster installs
- Faster script execution
- Better dependency resolution

</package_management>

<common_patterns>

### Common Patterns

#### REST API with SQLite

```typescript
import { Database } from "bun:sqlite";

const db = new Database("app.db");
db.exec(`
  CREATE TABLE IF NOT EXISTS posts (
    id TEXT PRIMARY KEY,
    title TEXT NOT NULL,
    content TEXT NOT NULL,
    created_at TEXT NOT NULL
  )
`);

Bun.serve({
  routes: {
    "/api/posts": {
      GET: () => {
        const posts = db.query("SELECT * FROM posts").all();
        return Response.json(posts);
      },
      POST: async (req) => {
        const post = await req.json();
        const id = crypto.randomUUID();
        db.query(
          "INSERT INTO posts (id, title, content, created_at) VALUES (?, ?, ?, ?)"
        ).run(id, post.title, post.content, new Date().toISOString());
        return Response.json({ id, ...post }, { status: 201 });
      },
    },
    "/api/posts/:id": (req) => {
      const post = db.query("SELECT * FROM posts WHERE id = ?").get(req.params.id);
      if (!post) {
        return new Response("Not Found", { status: 404 });
      }
      return Response.json(post);
    },
  },
});
```

#### Environment Variables

```typescript
// Access environment variables
const port = process.env.PORT || 3000;
const dbUrl = process.env.DATABASE_URL || "sqlite://app.db";

// Use in server
Bun.serve({
  port: parseInt(port),
  // ...
});
```

#### Error Handling Pattern

```typescript
Bun.serve({
  routes: {
    "/api/data": async (req) => {
      try {
        const data = await fetchData();
        return Response.json(data);
      } catch (error) {
        if (error instanceof ValidationError) {
          return Response.json({ error: error.message }, { status: 400 });
        }
        console.error("Unexpected error:", error);
        return Response.json(
          { error: "Internal Server Error" },
          { status: 500 }
        );
      }
    },
  },
  error(error) {
    console.error("Server error:", error);
    return new Response("Internal Server Error", { status: 500 });
  },
});
```

#### WebSocket Support

```typescript
Bun.serve({
  fetch(req, server) {
    if (server.upgrade(req)) {
      return; // Upgrade successful
    }
    return new Response("Expected WebSocket", { status: 400 });
  },
  websocket: {
    message(ws, message) {
      ws.send(`Echo: ${message}`);
    },
    open(ws) {
      console.log("WebSocket opened");
    },
    close(ws) {
      console.log("WebSocket closed");
    },
  },
});
```

</common_patterns>

<anti_patterns>

### Anti-Patterns to Avoid

#### ❌ Using Node.js APIs When Bun Has Native Alternatives

```typescript
// ❌ Avoid: Using Node.js http module
import http from "node:http";
http.createServer((req, res) => {
  res.end("Hello");
}).listen(3000);

// ✅ Use: Bun.serve()
Bun.serve({
  port: 3000,
  fetch(req) {
    return new Response("Hello");
  },
});
```

#### ❌ Not Using Prepared Statements

```typescript
// ❌ Avoid: Preparing queries on every call
const user = db.query("SELECT * FROM users WHERE id = ?").get(userId);

// ✅ Use: Prepared statements
const getUser = db.prepare("SELECT * FROM users WHERE id = ?");
const user = getUser.get(userId);
```

#### ❌ Not Enabling WAL Mode for SQLite

```typescript
// ❌ Avoid: Default journal mode (slower for concurrent operations)
const db = new Database("app.db");

// ✅ Use: WAL mode for better concurrency
const db = new Database("app.db");
db.exec("PRAGMA journal_mode = WAL;");
```

#### ❌ Not Using Transactions for Related Operations

```typescript
// ❌ Avoid: Multiple separate operations
db.query("INSERT INTO users (name) VALUES (?)").run("John");
db.query("INSERT INTO profiles (user_id) VALUES (?)").run(userId);

// ✅ Use: Transactions for atomicity
const createUserWithProfile = db.transaction((name) => {
  const userId = db.query("INSERT INTO users (name) VALUES (?) RETURNING id").get(name);
  db.query("INSERT INTO profiles (user_id) VALUES (?)").run(userId);
});
```

#### ❌ Not Closing Database Connections

```typescript
// ❌ Avoid: Leaving connections open
const db = new Database("app.db");
// ... use database
// Connection may not be closed properly

// ✅ Use: 'using' statement for automatic cleanup
{
  using db = new Database("app.db");
  // ... use database
  // Automatically closed when block exits
}
```

#### ❌ Loading Large Files into Memory

```typescript
// ❌ Avoid: Loading entire file into memory
const file = Bun.file("./large-file.txt");
const content = await file.text(); // May use too much memory

// ✅ Use: Streaming for large files
const file = Bun.file("./large-file.txt");
const stream = file.stream();
// Process stream in chunks
```

</anti_patterns>

## ⚠️ Critical Reminder

**Always follow the `<critical_validations>` section at the top of this document. These principles and best practices are mandatory and must be enforced in all Bun.js development work. Leverage Bun's native capabilities for maximum performance and developer experience.**

