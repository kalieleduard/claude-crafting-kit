---
name: typescript
description: Expert in TypeScript type system, strict mode, generics, utility types, and advanced type patterns. Specializes in type safety, inference, and leveraging TypeScript's powerful type system for robust applications. **ALWAYS use when writing TypeScript code, designing type-safe APIs, working with generics, or configuring TypeScript projects.** Use proactively to ensure type safety, proper inference, and adherence to TypeScript best practices. Examples - "write TypeScript code", "design type-safe API", "use generics", "configure TypeScript", "improve type safety", "use utility types".
---

<critical_validations>
- Strict Mode: Always enable strict mode in tsconfig.json; use `"strict": true` to enable all strict checks; this is the foundation of type safety.
- Type Safety: Never use `any` unless absolutely necessary; prefer `unknown` for truly dynamic values; use type guards to narrow types.
- Type Inference: Leverage TypeScript's type inference; only add explicit types when necessary for clarity or when inference fails.
- Generics: Use generics for reusable, type-safe code; constrain generic parameters when appropriate; prefer generic functions over overloads when possible.
- Utility Types: Use built-in utility types (Pick, Omit, Partial, Record, etc.) instead of manually creating similar types.
- Null Safety: Always enable `strictNullChecks`; handle `null` and `undefined` explicitly; use optional chaining and nullish coalescing.
- Configuration: Configure tsconfig.json properly for the target environment; use appropriate module system and resolution strategy.
</critical_validations>

<related_skills>

→ Use `backend` skill for Clean Architecture and project structure when building TypeScript backend services

→ Use `code-quality` skill for SOLID principles and Clean Code patterns when designing TypeScript applications

→ Use `code-standards` skill for code formatting and style standards when writing TypeScript code

→ Use `bun` skill for Bun.js-specific TypeScript configuration and patterns when using Bun runtime

</related_skills>

<strict_mode>

### Strict Mode Configuration

Strict mode is essential for type safety. Always enable it in `tsconfig.json`.

#### Recommended tsconfig.json

```json
{
  "compilerOptions": {
    "strict": true,
    "target": "ESNext",
    "module": "ESNext",
    "moduleResolution": "bundler",
    "lib": ["ESNext"],
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true
  }
}
```

#### Strict Mode Flags

The `"strict": true` flag enables all of the following:

- **`strictNullChecks`**: `null` and `undefined` are separate types
- **`strictFunctionTypes`**: Functions are checked more strictly
- **`strictBindCallApply`**: `bind`, `call`, and `apply` are strictly typed
- **`strictPropertyInitialization`**: Class properties must be initialized
- **`noImplicitThis`**: `this` must be explicitly typed
- **`alwaysStrict`**: Parse in strict mode and emit "use strict"
- **`noImplicitAny`**: Disallow implicit `any` types
- **`noUnusedLocals`**: Report errors on unused local variables
- **`noUnusedParameters`**: Report errors on unused parameters
- **`noImplicitReturns`**: Ensure all code paths return a value
- **`noFallthroughCasesInSwitch`**: Report errors for fallthrough cases

#### Why Strict Mode Matters

```typescript
// ❌ Without strictNullChecks: No error, but runtime crash possible
function getLength(str: string): number {
  return str.length;
}
getLength(null); // No compile error, but runtime error

// ✅ With strictNullChecks: Compile-time error
function getLength(str: string): number {
  return str.length;
}
getLength(null); // Error: Argument of type 'null' is not assignable to parameter of type 'string'
```

</strict_mode>

<type_safety>

### Type Safety Best Practices

#### Avoid `any`, Prefer `unknown`

```typescript
// ❌ Avoid: Using any
function processData(data: any) {
  return data.value.toUpperCase(); // No type safety
}

// ✅ Use: unknown with type guards
function processData(data: unknown) {
  if (typeof data === "object" && data !== null && "value" in data) {
    const value = (data as { value: string }).value;
    return value.toUpperCase();
  }
  throw new Error("Invalid data");
}
```

#### Type Guards

Use type guards to narrow types safely:

```typescript
// Type predicate function
function isString(value: unknown): value is string {
  return typeof value === "string";
}

function process(value: unknown) {
  if (isString(value)) {
    // TypeScript knows value is string here
    return value.toUpperCase();
  }
  throw new Error("Expected string");
}
```

#### Null Safety

Always handle `null` and `undefined` explicitly:

```typescript
// ❌ Avoid: Ignoring null/undefined
function getName(user: User): string {
  return user.name; // What if name is undefined?
}

// ✅ Use: Explicit null handling
function getName(user: User): string {
  if (user.name === undefined || user.name === null) {
    throw new Error("Name is required");
  }
  return user.name;
}

// ✅ Or: Use optional chaining and nullish coalescing
function getName(user: User): string {
  return user.name ?? "Unknown";
}
```

#### Type Assertions

Use type assertions sparingly and only when you're certain:

```typescript
// ❌ Avoid: Unsafe type assertion
const value = data as string; // Assumes data is string

// ✅ Use: Type guard first, then assertion
function isString(value: unknown): value is string {
  return typeof value === "string";
}

if (isString(data)) {
  const value = data; // TypeScript knows it's string
}
```

</type_safety>

<generics>

### Generics Best Practices

Generics enable reusable, type-safe code. Use them for functions, classes, and types that work with multiple types.

#### Basic Generic Functions

```typescript
// ✅ Good: Generic identity function
function identity<T>(arg: T): T {
  return arg;
}

const str = identity("hello"); // string
const num = identity(42); // number
```

#### Generic Constraints

Constrain generic parameters when appropriate:

```typescript
// ✅ Good: Constrained generic
function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key];
}

const user = { name: "John", age: 30 };
const name = getProperty(user, "name"); // string
const age = getProperty(user, "age"); // number
```

#### Generic Classes

```typescript
// ✅ Good: Generic class
class Box<T> {
  private contents: T;
  constructor(value: T) {
    this.contents = value;
  }
  get(): T {
    return this.contents;
  }
}

const stringBox = new Box("hello");
const numberBox = new Box(42);
```

#### Generic Interfaces

```typescript
// ✅ Good: Generic interface
interface Container<T> {
  value: T;
  getValue(): T;
}

class StringContainer implements Container<string> {
  value: string;
  getValue(): string {
    return this.value;
  }
}
```

#### Generic Type Aliases

```typescript
// ✅ Good: Generic type alias
type OrNull<T> = T | null;
type OneOrMany<T> = T | T[];

const value: OrNull<string> = "hello";
const values: OneOrMany<number> = [1, 2, 3];
```

#### Prefer Generics Over Overloads

```typescript
// ❌ Avoid: Function overloads when generics work
function process(value: string): string;
function process(value: number): number;
function process(value: string | number): string | number {
  return value;
}

// ✅ Use: Generic function
function process<T extends string | number>(value: T): T {
  return value;
}
```

#### NoInfer Utility Type

Use `NoInfer` to prevent inference for specific parameters:

```typescript
function createStreetLight<C extends string>(
  colors: C[],
  defaultColor?: NoInfer<C>
) {
  // ...
}

createStreetLight(["red", "yellow", "green"], "blue");
// Error: "blue" is not assignable to "red" | "yellow" | "green"
```

</generics>

<utility_types>

### Utility Types

TypeScript provides built-in utility types. Use them instead of manually creating similar types.

#### Pick<Type, Keys>

Select a subset of properties:

```typescript
interface User {
  id: string;
  name: string;
  email: string;
  password: string;
}

type PublicUser = Pick<User, "id" | "name" | "email">;
// { id: string; name: string; email: string }
```

#### Omit<Type, Keys>

Exclude properties from a type:

```typescript
type UserWithoutPassword = Omit<User, "password">;
// { id: string; name: string; email: string }
```

#### Partial<Type>

Make all properties optional:

```typescript
type PartialUser = Partial<User>;
// { id?: string; name?: string; email?: string; password?: string }
```

#### Required<Type>

Make all properties required:

```typescript
type RequiredUser = Required<PartialUser>;
// Back to all required
```

#### Record<Keys, Type>

Create an object type with specific keys and values:

```typescript
type UserRoles = Record<string, boolean>;
// { [key: string]: boolean }

type StatusMap = Record<"pending" | "approved" | "rejected", string>;
// { pending: string; approved: string; rejected: string }
```

#### Readonly<Type>

Make all properties readonly:

```typescript
type ReadonlyUser = Readonly<User>;
// All properties are readonly
```

#### Extract<Type, Union>

Extract types from a union:

```typescript
type T0 = Extract<"a" | "b" | "c", "a" | "f">;
// "a"
```

#### Exclude<Type, Union>

Exclude types from a union:

```typescript
type T0 = Exclude<"a" | "b" | "c", "a">;
// "b" | "c"
```

#### ReturnType<Type>

Extract return type from a function:

```typescript
function getUser(): User {
  return { id: "1", name: "John", email: "john@example.com", password: "secret" };
}

type UserReturn = ReturnType<typeof getUser>;
// User
```

#### Parameters<Type>

Extract parameter types from a function:

```typescript
function createUser(name: string, age: number): User {
  // ...
}

type CreateUserParams = Parameters<typeof createUser>;
// [name: string, age: number]
```

#### ConstructorParameters<Type>

Extract constructor parameter types:

```typescript
class User {
  constructor(name: string, age: number) {}
}

type UserConstructorParams = ConstructorParameters<typeof User>;
// [name: string, age: number]
```

#### InstanceType<Type>

Extract instance type from a constructor:

```typescript
type UserInstance = InstanceType<typeof User>;
// User
```

#### ThisParameterType<Type>

Extract `this` parameter type:

```typescript
function toHex(this: Number) {
  return this.toString(16);
}

type ThisType = ThisParameterType<typeof toHex>;
// Number
```

#### ThisType<Type>

Mark contextual `this` type:

```typescript
type ObjectDescriptor<D, M> = {
  data?: D;
  methods?: M & ThisType<D & M>;
};

function makeObject<D, M>(desc: ObjectDescriptor<D, M>): D & M {
  let data: object = desc.data || {};
  let methods: object = desc.methods || {};
  return { ...data, ...methods } as D & M;
}

let obj = makeObject({
  data: { x: 0, y: 0 },
  methods: {
    moveBy(dx: number, dy: number) {
      this.x += dx; // Strongly typed this
      this.y += dy; // Strongly typed this
    },
  },
});
```

</utility_types>

<advanced_types>

### Advanced Type Patterns

#### Conditional Types

Use conditional types for type transformations:

```typescript
type TypeName<T> = T extends string
  ? "string"
  : T extends number
  ? "number"
  : T extends boolean
  ? "boolean"
  : T extends undefined
  ? "undefined"
  : T extends Function
  ? "function"
  : "object";

type T0 = TypeName<string>; // "string"
type T1 = TypeName<number>; // "number"
```

#### Mapped Types

Create new types by transforming properties:

```typescript
// Make all properties optional
type Partial<T> = {
  [K in keyof T]?: T[K];
};

// Make all properties readonly
type Readonly<T> = {
  readonly [K in keyof T]: T[K];
};

// Remove readonly modifier
type Writable<T> = {
  -readonly [K in keyof T]: T[K];
};
```

#### Template Literal Types

Use template literal types for string manipulation:

```typescript
type EventName<T extends string> = `on${Capitalize<T>}`;
type ClickEvent = EventName<"click">; // "onClick"

type Greeting = "hello";
type EnthusiasticGreeting = `${Uppercase<Greeting>}!`; // "HELLO!"
```

#### Distributive Conditional Types

Conditional types distribute over union types:

```typescript
type ToArray<T> = T extends any ? T[] : never;
type StrArrOrNumArr = ToArray<string | number>;
// string[] | number[]
```

#### Tuple Types

Use tuples for fixed-length arrays:

```typescript
type StringNumberPair = [string, number];
const pair: StringNumberPair = ["hello", 42];

// Rest elements
type StringNumberBooleans = [string, number, ...boolean[]];
type StringBooleansNumber = [string, ...boolean[], number];
```

#### Template String Type Inference

TypeScript can infer types from template strings:

```typescript
declare function foo<V extends string>(arg: `*${V}*`): V;

let x1 = foo("*hello*"); // "hello"
let x2 = foo("**hello**"); // "*hello*"
```

</advanced_types>

<type_inference>

### Type Inference Best Practices

TypeScript's type inference is powerful. Leverage it instead of adding explicit types everywhere.

#### Let TypeScript Infer

```typescript
// ✅ Good: Let TypeScript infer
const name = "John"; // string
const age = 30; // number
const isActive = true; // boolean

// ❌ Avoid: Unnecessary explicit types
const name: string = "John";
const age: number = 30;
const isActive: boolean = true;
```

#### Function Return Type Inference

```typescript
// ✅ Good: Let TypeScript infer return type
function add(a: number, b: number) {
  return a + b; // Inferred as number
}

// ✅ Good: Explicit return type when needed for clarity
function processUser(user: User): ProcessedUser {
  // Complex logic
  return processed;
}
```

#### Contextual Typing

TypeScript infers types from context:

```typescript
// ✅ Good: Contextual typing
declare function map<T, U>(f: (t: T) => U, ts: T[]): U[];
let sns = map((n) => n.toString(), [1, 2, 3]);
// TypeScript infers n is number from the array
```

#### Best Common Type

TypeScript finds the best common type for arrays:

```typescript
let x = [0, 1, null];
// Inferred as (number | null)[]
```

#### Generic Type Inference

```typescript
// ✅ Good: Generic inference
function firstElement<Type>(arr: Type[]): Type | undefined {
  return arr[0];
}

const s = firstElement(["a", "b", "c"]); // string
const n = firstElement([1, 2, 3]); // number
```

</type_inference>

<interfaces_vs_types>

### Interfaces vs Type Aliases

#### Use Interfaces For

- Object shapes that might be extended
- Public APIs
- Declaration merging

```typescript
// ✅ Good: Interface for extensible object shape
interface User {
  id: string;
  name: string;
}

interface Admin extends User {
  permissions: string[];
}
```

#### Use Type Aliases For

- Unions and intersections
- Mapped types
- Utility types
- Complex type transformations

```typescript
// ✅ Good: Type alias for union
type Status = "pending" | "approved" | "rejected";

// ✅ Good: Type alias for complex type
type UserWithStatus = User & { status: Status };
```

#### Declaration Merging

Interfaces support declaration merging:

```typescript
interface User {
  name: string;
}

interface User {
  age: number;
}

// User now has both name and age
```

#### When to Use Which

- **Interfaces**: When you need declaration merging or are defining object shapes
- **Type Aliases**: When you need unions, intersections, or complex type transformations

</interfaces_vs_types>

<classes>

### Classes Best Practices

#### Property Initialization

With `strictPropertyInitialization`, properties must be initialized:

```typescript
// ❌ Error: Property 'name' has no initializer
class User {
  name: string;
}

// ✅ Good: Initialize in constructor
class User {
  name: string;
  constructor(name: string) {
    this.name = name;
  }
}

// ✅ Good: Use definite assignment assertion when appropriate
class User {
  name!: string; // Will be assigned before use
  initialize(name: string) {
    this.name = name;
  }
}
```

#### Access Modifiers

Use access modifiers appropriately:

```typescript
class User {
  public id: string; // Public (default)
  private password: string; // Private
  protected email: string; // Protected
  readonly createdAt: Date; // Readonly
}
```

#### Abstract Classes

Use abstract classes for base implementations:

```typescript
abstract class Animal {
  abstract makeSound(): void;
  move(): void {
    console.log("Moving...");
  }
}

class Dog extends Animal {
  makeSound(): void {
    console.log("Woof!");
  }
}
```

#### Generic Classes

Use generics for reusable class definitions:

```typescript
class Container<T> {
  private value: T;
  constructor(value: T) {
    this.value = value;
  }
  get(): T {
    return this.value;
  }
  set(value: T): void {
    this.value = value;
  }
}
```

</classes>

<functions>

### Functions Best Practices

#### Function Types

Use function type annotations:

```typescript
// ✅ Good: Function type
type Handler = (event: Event) => void;

// ✅ Good: Function with this context
function handler(this: HTMLElement, event: Event): void {
  // ...
}
```

#### Optional and Default Parameters

```typescript
// ✅ Good: Optional parameter
function greet(name: string, title?: string): string {
  return title ? `${title} ${name}` : name;
}

// ✅ Good: Default parameter
function greet(name: string, title: string = "Mr"): string {
  return `${title} ${name}`;
}
```

#### Rest Parameters

```typescript
// ✅ Good: Rest parameters
function sum(...numbers: number[]): number {
  return numbers.reduce((a, b) => a + b, 0);
}
```

#### Function Overloads

Use overloads when you need different signatures:

```typescript
// ✅ Good: Function overloads
function process(value: string): string;
function process(value: number): number;
function process(value: string | number): string | number {
  return value;
}
```

#### Generic Functions

Prefer generics over overloads when possible:

```typescript
// ✅ Good: Generic function
function identity<T>(arg: T): T {
  return arg;
}
```

</functions>

<modules>

### Module System Best Practices

#### ES Modules

Prefer ES modules:

```typescript
// ✅ Good: ES module export
export function add(a: number, b: number): number {
  return a + b;
}

// ✅ Good: ES module import
import { add } from "./math";
```

#### Type-Only Imports/Exports

Use type-only imports for types:

```typescript
// ✅ Good: Type-only import
import type { User } from "./types";
import { createUser } from "./user";

// ✅ Good: Type-only export
export type { User, Admin };
```

#### Namespace Exports

Use namespaces for organizing related exports:

```typescript
// ✅ Good: Namespace
export namespace Math {
  export function add(a: number, b: number): number {
    return a + b;
  }
  export function subtract(a: number, b: number): number {
    return a - b;
  }
}
```

</modules>

<common_patterns>

### Common Patterns

#### Discriminated Unions

Use discriminated unions for type-safe state:

```typescript
type Result<T> =
  | { success: true; data: T }
  | { success: false; error: string };

function processResult<T>(result: Result<T>): void {
  if (result.success) {
    console.log(result.data);
  } else {
    console.error(result.error);
  }
}
```

#### Builder Pattern

```typescript
class QueryBuilder {
  private query: string = "";
  select(fields: string[]): this {
    this.query += `SELECT ${fields.join(", ")}`;
    return this;
  }
  from(table: string): this {
    this.query += ` FROM ${table}`;
    return this;
  }
  build(): string {
    return this.query;
  }
}
```

#### Factory Pattern

```typescript
interface Animal {
  makeSound(): void;
}

class Dog implements Animal {
  makeSound(): void {
    console.log("Woof!");
  }
}

class Cat implements Animal {
  makeSound(): void {
    console.log("Meow!");
  }
}

function createAnimal(type: "dog" | "cat"): Animal {
  switch (type) {
    case "dog":
      return new Dog();
    case "cat":
      return new Cat();
  }
}
```

#### Type-Safe Event System

```typescript
interface EventMap {
  click: { x: number; y: number };
  keypress: { key: string };
}

class EventEmitter {
  private listeners: { [K in keyof EventMap]?: Array<(event: EventMap[K]) => void> } = {};
  on<K extends keyof EventMap>(event: K, handler: (data: EventMap[K]) => void): void {
    if (!this.listeners[event]) {
      this.listeners[event] = [];
    }
    this.listeners[event]!.push(handler);
  }
  emit<K extends keyof EventMap>(event: K, data: EventMap[K]): void {
    this.listeners[event]?.forEach((handler) => handler(data));
  }
}
```

</common_patterns>

<anti_patterns>

### Anti-Patterns to Avoid

#### ❌ Using `any` Instead of `unknown`

```typescript
// ❌ Avoid: Using any
function process(data: any) {
  return data.value.toUpperCase();
}

// ✅ Use: unknown with type guards
function process(data: unknown) {
  if (typeof data === "object" && data !== null && "value" in data) {
    const value = (data as { value: string }).value;
    return value.toUpperCase();
  }
  throw new Error("Invalid data");
}
```

#### ❌ Ignoring Null/Undefined

```typescript
// ❌ Avoid: Ignoring null/undefined
function getName(user: User): string {
  return user.name; // What if name is undefined?
}

// ✅ Use: Explicit null handling
function getName(user: User): string {
  return user.name ?? "Unknown";
}
```

#### ❌ Unnecessary Type Annotations

```typescript
// ❌ Avoid: Unnecessary explicit types
const name: string = "John";
const age: number = 30;

// ✅ Use: Let TypeScript infer
const name = "John";
const age = 30;
```

#### ❌ Using Function Overloads When Generics Work

```typescript
// ❌ Avoid: Overloads when generics work
function process(value: string): string;
function process(value: number): number;
function process(value: string | number): string | number {
  return value;
}

// ✅ Use: Generic function
function process<T extends string | number>(value: T): T {
  return value;
}
```

#### ❌ Not Using Utility Types

```typescript
// ❌ Avoid: Manually creating utility types
type PartialUser = {
  id?: string;
  name?: string;
  email?: string;
};

// ✅ Use: Built-in utility types
type PartialUser = Partial<User>;
```

#### ❌ Unsafe Type Assertions

```typescript
// ❌ Avoid: Unsafe type assertion
const value = data as string;

// ✅ Use: Type guard first
function isString(value: unknown): value is string {
  return typeof value === "string";
}

if (isString(data)) {
  const value = data; // TypeScript knows it's string
}
```

#### ❌ Disabling Strict Mode

```typescript
// ❌ Avoid: Disabling strict mode
{
  "compilerOptions": {
    "strict": false
  }
}

// ✅ Use: Always enable strict mode
{
  "compilerOptions": {
    "strict": true
  }
}
```

#### ❌ Using `Function` Type

```typescript
// ❌ Avoid: Function type
function call(func: Function): void {
  func();
}

// ✅ Use: Specific function signature
function call(func: () => void): void {
  func();
}
```

</anti_patterns>

## ⚠️ Critical Reminder

**Always follow the `<critical_validations>` section at the top of this document. These TypeScript principles and best practices are mandatory and must be enforced in all TypeScript development work. Enable strict mode, avoid `any`, use generics appropriately, and leverage TypeScript's powerful type system for maximum type safety.**

