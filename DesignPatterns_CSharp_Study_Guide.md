# Design Patterns in C# — Complete Interview Study Guide

> **How to use this guide:** Read Part 1 first to build the right mental model. Then study each pattern using the consistent template. Use the comparisons section before interviews. Revise using the final cheat sheet. Every code example is minimal, realistic, and interview-ready.

---

## Table of Contents

1. [Part 1 — Fundamentals](#part-1--fundamentals)
2. [Part 2 — Creational Patterns](#part-2--creational-patterns)
   - [Singleton](#1-singleton)
   - [Factory Method](#2-factory-method)
   - [Abstract Factory](#3-abstract-factory)
   - [Builder](#4-builder)
   - [Prototype](#5-prototype)
3. [Part 3 — Structural Patterns](#part-3--structural-patterns)
   - [Adapter](#6-adapter)
   - [Bridge](#7-bridge)
   - [Composite](#8-composite)
   - [Decorator](#9-decorator)
   - [Facade](#10-facade)
   - [Flyweight](#11-flyweight)
   - [Proxy](#12-proxy)
4. [Part 4 — Behavioral Patterns](#part-4--behavioral-patterns)
   - [Chain of Responsibility](#13-chain-of-responsibility)
   - [Command](#14-command)
   - [Interpreter](#15-interpreter)
   - [Iterator](#16-iterator)
   - [Mediator](#17-mediator)
   - [Memento](#18-memento)
   - [Observer](#19-observer)
   - [State](#20-state)
   - [Strategy](#21-strategy)
   - [Template Method](#22-template-method)
   - [Visitor](#23-visitor)
5. [Part 5 — Essential Interview Comparisons](#part-5--essential-interview-comparisons)
6. [Part 6 — Advanced Interview Topics](#part-6--advanced-interview-topics)
7. [Final Cheat Sheet](#final-cheat-sheet)

---

# Part 1 — Fundamentals

## What Are Design Patterns?

Design patterns are **proven, reusable solutions to commonly occurring problems in software design**. They are not finished code you copy-paste — they are templates or blueprints you adapt to your situation.

- Coined by the "Gang of Four" (GoF): Erich Gamma, Richard Helm, Ralph Johnson, John Vlissides in their 1994 book *Design Patterns: Elements of Reusable Object-Oriented Software*.
- There are **23 original GoF patterns** grouped into three categories: Creational, Structural, and Behavioral.
- Patterns describe the **relationship and interaction between classes and objects**, not the specific algorithm.

**Simple analogy:** A design pattern is like an architectural blueprint for a building. The blueprint says "put the kitchen near the dining room for convenience" — it does not tell you what color to paint the walls. You adapt the blueprint to your specific project.

---

## Benefits and Trade-offs

| Benefit | Explanation |
|---|---|
| **Shared vocabulary** | Saying "use the Strategy pattern" communicates a complete design idea instantly to any developer |
| **Proven solutions** | Patterns have been battle-tested across thousands of codebases |
| **Maintainability** | Pattern-based code is easier to change because responsibilities are well-separated |
| **Extensibility** | Most patterns are designed around the Open/Closed Principle |
| **Testability** | Patterns naturally introduce abstractions that make unit testing easier |

| Trade-off | Explanation |
|---|---|
| **Complexity** | Patterns add classes and indirection — overkill for simple problems |
| **Over-engineering** | Applying patterns prematurely makes code harder to read |
| **Learning curve** | Junior developers may find pattern-heavy code harder to follow |
| **Performance** | Some patterns (e.g., Proxy, Decorator) add indirection that can impact hot paths |

---

## Relationship to SOLID Principles

Design patterns are practical implementations of SOLID principles:

| Pattern | SOLID Principle(s) it implements |
|---|---|
| **Strategy** | OCP — swap algorithms without modifying callers |
| **Factory Method** | DIP — depend on abstractions, not concrete constructors |
| **Decorator** | OCP, SRP — add behavior without modifying existing classes |
| **Observer** | OCP, DIP — publishers don't know about subscribers |
| **Proxy** | SRP — separates access control from business logic |
| **Command** | SRP, OCP — encapsulates action as an object |
| **Composite** | LSP — treat individual and group objects uniformly |
| **Repository** | DIP — high-level code depends on IRepository, not concrete DB |

> Patterns do not replace SOLID thinking — they are concrete shapes that SOLID principles take in real code.

---

## When NOT to Use Design Patterns

This is a critical interview topic. Knowing when **not** to use a pattern shows seniority.

- **YAGNI (You Ain't Gonna Need It):** Do not add a pattern because you *might* need flexibility later. Add it when you have a concrete second use case.
- **Simple problems:** A 10-line function does not need a Command pattern.
- **Prototype-stage code:** Over-engineering early slows down exploration.
- **Team unfamiliarity:** A pattern no one else on the team knows will cause confusion, not clarity.
- **Performance-critical paths:** Extra indirection from patterns like Proxy and Decorator has real cost in tight loops.

> **Interview answer:** "I apply patterns when I see the problem they solve. Using a pattern 'just in case' is over-engineering. The best code is the simplest code that works correctly and is easy to change."

---

## Classification Overview

| Category | Focus | Key Question | Patterns |
|---|---|---|---|
| **Creational** | Object creation | "How do I create objects flexibly?" | Singleton, Factory Method, Abstract Factory, Builder, Prototype |
| **Structural** | Object composition | "How do I assemble objects into larger structures?" | Adapter, Bridge, Composite, Decorator, Facade, Flyweight, Proxy |
| **Behavioral** | Communication between objects | "How do objects interact and share responsibility?" | Chain of Responsibility, Command, Interpreter, Iterator, Mediator, Memento, Observer, State, Strategy, Template Method, Visitor |

**Memory mnemonic for categories:** **C**reate things, **S**tructure things, **B**ehave together — **C-S-B**.

> **Summary:** Design patterns are vocabulary and blueprints for solving recurring OOP design problems. They implement SOLID principles in practice. Use them when the problem they solve is present — not speculatively. Know all three categories: Creational (5), Structural (7), Behavioral (11).

---

# Part 2 — Creational Patterns

> **Category intent:** Control how objects are created — decouple client code from the `new` keyword and concrete types.

---

## 1. Singleton

#### Intent
Ensure a class has **only one instance** and provide a global access point to it.

#### Problem It Solves
- Multiple instances of a resource-heavy object waste memory (e.g., database connection managers, configuration loaders).
- Inconsistent state when two parts of the app each create their own instance of something that should be shared.

#### When to Use
- Shared, stateful resources: configuration manager, logging service, connection pool.
- Exactly one instance must coordinate actions across the system.

#### When NOT to Use
- When global state makes code hard to test (singleton state bleeds between tests).
- When you are tempted to use it just to avoid passing parameters — use dependency injection instead.
- In multi-threaded code without proper locking.

#### Real-World Analogy
A country has exactly one president at a time. No matter who asks "who is the president?", they get a reference to the same person.

#### Enterprise Use Case
A `ConfigurationManager` that reads `appsettings.json` once and provides app-wide configuration — creating it multiple times would waste I/O and risk stale config.

#### C# Code Example

```csharp
public sealed class ConfigurationManager
{
    private static ConfigurationManager? _instance;
    private static readonly object _lock = new object();

    public string ConnectionString { get; private set; }

    // Private constructor — no one can call new ConfigurationManager()
    private ConfigurationManager()
    {
        // Simulate reading from appsettings.json
        ConnectionString = "Server=prod-db;Database=AppDb;";
    }

    public static ConfigurationManager Instance
    {
        get
        {
            if (_instance == null)
            {
                lock (_lock) // thread-safe: only one thread enters at a time
                {
                    if (_instance == null) // double-check after acquiring lock
                    {
                        _instance = new ConfigurationManager();
                    }
                }
            }
            return _instance;
        }
    }
}

// Usage
var config1 = ConfigurationManager.Instance;
var config2 = ConfigurationManager.Instance;
Console.WriteLine(ReferenceEquals(config1, config2)); // True — same object
Console.WriteLine(config1.ConnectionString);
```

#### Step-by-Step Explanation
1. `sealed` prevents subclassing (subclass could create a second instance).
2. `private` constructor blocks external `new` calls.
3. `_lock` object ensures only one thread can create the instance.
4. The double-check (`if (_instance == null)` twice) avoids the cost of locking after the instance exists.
5. Callers always go through `Instance` — guaranteed to return the same object.

#### Advantages
- Controlled access to the single instance.
- Lazy initialization — instance created only when first needed.
- Thread-safe with double-checked locking.

#### Disadvantages
- Introduces global state — makes unit testing harder.
- Violates SRP (manages its own lifecycle AND provides business value).
- Hidden dependencies — hard to see which classes use the singleton.

#### Common Interview Q&A

**Q: How do you make a Singleton thread-safe in C#?**
> Use double-checked locking with a `lock` object, or use `Lazy<T>` which is thread-safe by default: `private static readonly Lazy<ConfigurationManager> _instance = new(() => new ConfigurationManager());`

**Q: What is the difference between Singleton pattern and a static class?**
> A Singleton is an object — it can implement interfaces, be passed as a parameter, and be subclassed. A static class cannot be instantiated, cannot implement interfaces, and cannot be injected. Singleton is preferred when you need an object that participates in OOP.

**Q: How do you test code that uses a Singleton?**
> Extract an interface (e.g., `IConfigurationManager`), inject it via constructor, and register the singleton with the DI container. The Singleton pattern in modern .NET is best handled by `AddSingleton<>()` in the DI container rather than the classic GoF implementation.

#### Common Mistakes
- Forgetting thread-safety — naive `if (_instance == null) _instance = new ...` is a race condition.
- Using Singleton when DI with `AddSingleton` achieves the same result with better testability.
- Making fields mutable — shared mutable state causes bugs in concurrent code.

#### Related Patterns
- **Facade** — often implemented as a Singleton.
- **Factory Method** — Singleton controls its own creation; Factory delegates creation.

#### Key Takeaways
- One instance, global access, private constructor, thread-safe creation.
- Modern C#: prefer `services.AddSingleton<T>()` over the GoF implementation.
- Singleton = global state — use only when truly needed; inject via interface for testability.

---

## 2. Factory Method

#### Intent
Define an interface for creating an object, but let **subclasses decide which class to instantiate**. The factory method defers object creation to subclasses.

#### Problem It Solves
- Client code is littered with `new ConcreteType()` — tightly coupled to a specific implementation.
- You need to produce different types of objects based on context, without the client knowing which type.

#### When to Use
- The exact type of object to create is determined at runtime.
- You want subclasses to control what gets created.
- You need to centralize object creation logic.

#### When NOT to Use
- When there is only one concrete implementation — adds unnecessary abstraction.
- When a simple `if/switch` is readable and the type set is fixed and small.

#### Real-World Analogy
A logistics company (creator) defines a `createTransport()` method. Road logistics creates `Truck`. Sea logistics creates `Ship`. The delivery process is the same — only the transport type differs.

#### Enterprise Use Case
A payment processing system where `PaymentProcessorFactory` returns `StripeProcessor`, `PayPalProcessor`, or `BankTransferProcessor` based on the payment method selected at runtime.

#### C# Code Example

```csharp
// Product interface
public interface IPaymentProcessor
{
    void ProcessPayment(decimal amount);
}

// Concrete products
public class StripeProcessor : IPaymentProcessor
{
    public void ProcessPayment(decimal amount)
        => Console.WriteLine($"Stripe: processing ${amount}");
}

public class PayPalProcessor : IPaymentProcessor
{
    public void ProcessPayment(decimal amount)
        => Console.WriteLine($"PayPal: processing ${amount}");
}

// Creator — defines the factory method
public abstract class PaymentFactory
{
    public abstract IPaymentProcessor CreateProcessor(); // factory method

    public void ExecutePayment(decimal amount)
    {
        var processor = CreateProcessor(); // uses the factory method
        processor.ProcessPayment(amount);
    }
}

// Concrete creators — decide which product to make
public class StripeFactory : PaymentFactory
{
    public override IPaymentProcessor CreateProcessor() => new StripeProcessor();
}

public class PayPalFactory : PaymentFactory
{
    public override IPaymentProcessor CreateProcessor() => new PayPalProcessor();
}

// Usage
PaymentFactory factory = new StripeFactory();
factory.ExecutePayment(99.99m); // Stripe: processing $99.99

factory = new PayPalFactory();
factory.ExecutePayment(49.99m); // PayPal: processing $49.99
```

#### Step-by-Step Explanation
1. `IPaymentProcessor` is the product interface — clients code against this.
2. Concrete products (`StripeProcessor`, `PayPalProcessor`) implement the interface.
3. `PaymentFactory` declares the abstract `CreateProcessor()` — the factory method.
4. `ExecutePayment()` uses `CreateProcessor()` internally — it does not know which type is returned.
5. Concrete factories override `CreateProcessor()` to return their specific product.

#### Advantages
- Eliminates tight coupling between creator and concrete product.
- Follows OCP — add a new payment type by adding a new factory class.
- Centralizes object creation logic.

#### Disadvantages
- Introduces extra classes per product type — can become verbose.
- Client must choose the right factory — still coupled to the factory hierarchy.

#### Common Interview Q&A

**Q: What is the difference between Factory Method and a simple static factory?**
> A static factory is a single method that uses `if/switch` to create objects — it must be modified for every new type (violates OCP). Factory Method uses inheritance — you add a new factory subclass without touching existing code.

**Q: Where is Factory Method used in .NET?**
> `ILoggerFactory.CreateLogger<T>()`, `DbProviderFactory.CreateConnection()`, and ASP.NET Core's `IHttpClientFactory.CreateClient()` are all Factory Method implementations.

**Q: How does Factory Method support OCP?**
> You add a new product by adding a new concrete factory class. No existing factory class needs to change. The `ExecutePayment()` method in the base creator never changes.

#### Common Mistakes
- Confusing Factory Method (uses inheritance) with Abstract Factory (uses composition/object families).
- Making the factory method `static` — then you lose the ability to subclass and override.
- Returning concrete types instead of the interface from the factory method.

#### Related Patterns
- **Abstract Factory** — uses multiple factory methods to create a family of related objects.
- **Template Method** — Factory Method is a specialization of Template Method for object creation.

#### Key Takeaways
- Factory Method = abstract creator + overridable creation method.
- Callers work with the product interface, never the concrete type.
- Add new products by adding new factories — no modification to existing code.

---

## 3. Abstract Factory

#### Intent
Provide an interface for creating **families of related objects** without specifying their concrete classes.

#### Problem It Solves
- You need to create a set of related objects (e.g., Button + Checkbox + Dialog) that must work together.
- Concrete types within a family must be consistent — mixing Windows Button with Mac Checkbox would be wrong.

#### When to Use
- Your system needs to be independent of how its products are created.
- You need to enforce that related objects are always used together (a family/theme).
- You are building cross-platform or multi-tenant UI/infrastructure.

#### When NOT to Use
- When there is only one product family — Factory Method is simpler.
- When adding a new product type requires changing all factory interfaces — the extension cost is high.

#### Real-World Analogy
An IKEA store is an abstract factory. You pick a furniture style (Scandinavian, Industrial) and everything in that style — sofa, table, lamp — is designed to go together. You do not mix styles.

#### Enterprise Use Case
A database provider factory in an ORM. When targeting SQL Server, the factory produces `SqlConnection`, `SqlCommand`, `SqlDataAdapter`. Switch to PostgreSQL — the factory produces `NpgsqlConnection`, `NpgsqlCommand`, `NpgsqlDataAdapter`. Client code never changes.

#### C# Code Example

```csharp
// Abstract products
public interface IButton  { void Render(); }
public interface ICheckbox { void Render(); }

// Windows family
public class WindowsButton  : IButton   { public void Render() => Console.WriteLine("Windows Button"); }
public class WindowsCheckbox : ICheckbox { public void Render() => Console.WriteLine("Windows Checkbox"); }

// Mac family
public class MacButton   : IButton   { public void Render() => Console.WriteLine("Mac Button"); }
public class MacCheckbox : ICheckbox { public void Render() => Console.WriteLine("Mac Checkbox"); }

// Abstract factory — creates a FAMILY of related products
public interface IUiFactory
{
    IButton CreateButton();
    ICheckbox CreateCheckbox();
}

// Concrete factories — each produces a consistent family
public class WindowsUiFactory : IUiFactory
{
    public IButton CreateButton()   => new WindowsButton();
    public ICheckbox CreateCheckbox() => new WindowsCheckbox();
}

public class MacUiFactory : IUiFactory
{
    public IButton CreateButton()   => new MacButton();
    public ICheckbox CreateCheckbox() => new MacCheckbox();
}

// Client — uses the factory, never knows which OS
public class Application
{
    private readonly IButton _button;
    private readonly ICheckbox _checkbox;

    public Application(IUiFactory factory)
    {
        _button   = factory.CreateButton();
        _checkbox = factory.CreateCheckbox();
    }

    public void RenderUI()
    {
        _button.Render();
        _checkbox.Render();
    }
}

// Usage
IUiFactory factory = new WindowsUiFactory();
var app = new Application(factory);
app.RenderUI(); // Windows Button / Windows Checkbox
```

#### Step-by-Step Explanation
1. Two abstract product interfaces: `IButton` and `ICheckbox`.
2. Two concrete families: Windows (button + checkbox) and Mac (button + checkbox).
3. `IUiFactory` declares creation methods for every product in the family.
4. `WindowsUiFactory` and `MacUiFactory` each return their own family's products.
5. `Application` receives a factory via constructor — it only knows the interfaces, never the concrete types.

#### Advantages
- Guarantees product family consistency.
- Isolates concrete classes from the client.
- Easy to swap entire families — change one constructor argument.

#### Disadvantages
- Hard to add a new product type (e.g., `ISlider`) — requires updating every factory interface and implementation.
- Results in many classes — one per product per family.

#### Common Interview Q&A

**Q: What is the difference between Factory Method and Abstract Factory?**
> Factory Method uses inheritance — one factory method, one product. Abstract Factory uses composition — one factory object creates an entire family of related products. See the comparison section for the full table.

**Q: Where is Abstract Factory used in .NET?**
> `DbProviderFactory` in ADO.NET is the classic example. `IServiceCollection` (the ASP.NET Core DI container) acts as an abstract factory for registering and resolving families of services.

**Q: When would you add a new product type to an Abstract Factory?**
> You update the `IUiFactory` interface to add `CreateSlider()`. This is the main weakness — all existing concrete factories must implement the new method. This is why Abstract Factory works best when the product family is stable.

#### Common Mistakes
- Using Abstract Factory when you only have one product — Factory Method is enough.
- Mixing products from different families (Windows button with Mac checkbox).
- Adding too many products to a single factory — split into smaller factories if families diverge.

#### Related Patterns
- **Factory Method** — Abstract Factory is often built using Factory Methods.
- **Singleton** — Concrete factories are often Singletons since you only need one.

#### Key Takeaways
- Abstract Factory = factory for a family of related, consistent objects.
- Swap entire UI/DB/infrastructure families by swapping the factory.
- Weakness: adding new product types requires updating all factories.

---

## 4. Builder

#### Intent
Separate the **construction of a complex object** from its representation, so the same construction process can create different representations.

#### Problem It Solves
- A class with 10+ constructor parameters is unreadable and error-prone (which `null` goes where?).
- Object construction has multiple steps that must be executed in a specific order.
- You need to build different representations of the same type (e.g., plain text report vs HTML report).

#### When to Use
- Constructing objects with many optional parameters.
- Multi-step construction where the order matters.
- Building complex objects like SQL queries, HTML documents, HTTP requests.

#### When NOT to Use
- Simple objects with 2-3 parameters — just use a constructor.
- Immutable value objects — use record types or simple constructors in C#.

#### Real-World Analogy
Ordering a custom burger. You tell the cashier (director): "whole wheat bun, double patty, no onions, extra cheese, add jalapeños." Each step adds to the burger (the product). The kitchen (builder) assembles it. The same kitchen process can produce a veggie burger by using different steps.

#### Enterprise Use Case
Building complex SQL queries, HTTP requests (`HttpRequestMessage`), or email messages where you conditionally set headers, body, attachments, and recipients.

#### C# Code Example

```csharp
// Product
public class Email
{
    public string To      { get; set; } = "";
    public string Subject { get; set; } = "";
    public string Body    { get; set; } = "";
    public string? Cc     { get; set; }
    public bool IsHtml    { get; set; }

    public override string ToString() =>
        $"To: {To} | Subject: {Subject} | CC: {Cc ?? "none"} | HTML: {IsHtml}";
}

// Builder
public class EmailBuilder
{
    private readonly Email _email = new Email();

    public EmailBuilder To(string to)           { _email.To = to;           return this; }
    public EmailBuilder Subject(string subject) { _email.Subject = subject; return this; }
    public EmailBuilder Body(string body)       { _email.Body = body;       return this; }
    public EmailBuilder Cc(string cc)           { _email.Cc = cc;           return this; }
    public EmailBuilder AsHtml()                { _email.IsHtml = true;     return this; }

    public Email Build()
    {
        if (string.IsNullOrEmpty(_email.To)) throw new InvalidOperationException("'To' is required.");
        return _email;
    }
}

// Usage — fluent, readable, no parameter-order confusion
var email = new EmailBuilder()
    .To("alice@example.com")
    .Subject("Order Confirmed")
    .Body("<h1>Thank you!</h1>")
    .Cc("manager@example.com")
    .AsHtml()
    .Build();

Console.WriteLine(email);
```

#### Step-by-Step Explanation
1. `Email` is the product — it has many optional fields.
2. `EmailBuilder` holds an `Email` instance and exposes fluent methods.
3. Each method sets one field and returns `this` — enabling method chaining.
4. `Build()` validates required fields and returns the complete product.
5. Client code reads like a sentence — clear, order-independent, no positional confusion.

#### Advantages
- Eliminates telescoping constructor anti-pattern.
- Makes optional parameters explicit and readable.
- Can enforce required fields in `Build()`.
- Fluent interface improves readability.

#### Disadvantages
- More code than a simple constructor.
- The builder itself can become complex for very elaborate objects.
- Mutable during construction — the intermediate state is not usable.

#### Common Interview Q&A

**Q: What problem does Builder solve that a constructor with default parameters doesn't?**
> Default parameters have fixed positions — calling `new Email("alice", null, null, "subject", true)` is still confusing. Builder makes each parameter explicit by name. It also allows conditional steps (`.AsHtml()` only when needed) and validation at build time.

**Q: What is the difference between Builder and Factory patterns?**
> Factory patterns create objects in one step — you get a complete product immediately. Builder constructs objects step by step. Use Factory for simple creation; use Builder for complex, multi-step, or optional-heavy construction.

**Q: Where is Builder used in .NET?**
> `StringBuilder` is the classic C# example. `WebApplication.CreateBuilder()` in ASP.NET Core, `IHostBuilder`, `HttpRequestMessage` construction, and Entity Framework's query building are all Builder pattern applications.

#### Common Mistakes
- Making all fields required — defeats the purpose of a builder.
- Skipping validation in `Build()` — the builder can return invalid objects.
- Not returning `this` from each method — breaks the fluent chain.

#### Related Patterns
- **Factory Method** — creates in one step; Builder creates step-by-step.
- **Composite** — builders often build composite structures (e.g., a tree of UI nodes).

#### Key Takeaways
- Builder = step-by-step construction of complex objects, often with a fluent interface.
- Use it to replace telescoping constructors and improve readability.
- `Build()` is the final step — validates and returns the complete, ready-to-use product.

---

## 5. Prototype

#### Intent
Create new objects by **copying (cloning) an existing object**, avoiding the cost of creating from scratch and hiding the complexity of initialization.

#### Problem It Solves
- Creating an object from scratch is expensive (reads from DB, calls an API, heavy computation).
- You need many similar objects with slight variations — cloning is faster than repeated construction.
- The class of the object to create is not known until runtime.

#### When to Use
- Object initialization is costly and you need multiple similar instances.
- You need a copy of an object at a point in time (snapshot).
- The system should be independent of how products are created and represented.

#### When NOT to Use
- Simple objects — just call the constructor again.
- When deep copy semantics are unclear (complex object graphs with circular references).
- When the class has resources that should not be shared (e.g., file handles, network connections).

#### Real-World Analogy
A photocopier. Instead of retyping a 100-page document, you copy the existing one and make small changes on the copy. The original is untouched.

#### Enterprise Use Case
A document template system where a `DocumentTemplate` object contains default formatting, margins, fonts, and styles. When a user creates a new document, the system clones the template instead of re-fetching all settings from the database.

#### C# Code Example

```csharp
// Prototype interface (C# provides ICloneable but a typed interface is safer)
public interface IPrototype<T>
{
    T Clone();
}

// Prototype class
public class DocumentTemplate : IPrototype<DocumentTemplate>
{
    public string FontFamily  { get; set; } = "";
    public int    FontSize    { get; set; }
    public string PageSize    { get; set; } = "";
    public List<string> Tags  { get; set; } = new();

    public DocumentTemplate Clone()
    {
        return new DocumentTemplate
        {
            FontFamily = this.FontFamily,
            FontSize   = this.FontSize,
            PageSize   = this.PageSize,
            Tags       = new List<string>(this.Tags) // deep copy the list
        };
    }

    public override string ToString() =>
        $"Font: {FontFamily} {FontSize}pt | Page: {PageSize} | Tags: [{string.Join(", ", Tags)}]";
}

// Usage
var masterTemplate = new DocumentTemplate
{
    FontFamily = "Arial",
    FontSize   = 12,
    PageSize   = "A4",
    Tags       = new List<string> { "corporate", "standard" }
};

// Clone and customize — no DB call, no expensive init
var invoiceTemplate = masterTemplate.Clone();
invoiceTemplate.Tags.Add("invoice");

var reportTemplate = masterTemplate.Clone();
reportTemplate.FontSize = 10;
reportTemplate.Tags.Add("report");

Console.WriteLine(masterTemplate);   // Tags: [corporate, standard]
Console.WriteLine(invoiceTemplate);  // Tags: [corporate, standard, invoice]
Console.WriteLine(reportTemplate);   // FontSize: 10, Tags: [corporate, standard, report]
```

#### Step-by-Step Explanation
1. `IPrototype<T>` defines the `Clone()` contract — typed and safe.
2. `DocumentTemplate` holds all configuration for a document type.
3. `Clone()` creates a new instance — shallow-copies primitives and strings, deep-copies the `Tags` list.
4. Each clone is independent — modifying `invoiceTemplate.Tags` does not affect `masterTemplate`.
5. Multiple templates are created cheaply — no repeated initialization.

#### Advantages
- Avoids expensive re-initialization.
- Hides construction complexity from clients.
- Easily create variations by cloning and tweaking.

#### Disadvantages
- Deep copying complex objects (nested references, cycles) is tricky to get right.
- C#'s built-in `ICloneable` is untyped (`object Clone()`) — prefer your own typed interface.
- Hidden coupling: cloned objects share reference-type fields unless explicitly deep-copied.

#### Common Interview Q&A

**Q: What is the difference between shallow copy and deep copy?**
> A shallow copy copies field values. For value types and strings, this is a full copy. For reference types, it copies the reference — both original and clone point to the same object. A deep copy creates new instances of all reference-type fields. Prototype typically needs deep copy to ensure independence.

**Q: How does C# support cloning natively?**
> `ICloneable` interface has a `Clone()` method returning `object`. It is generally considered too weakly typed. For records, `with` expressions create a shallow copy: `var copy = original with { FontSize = 10 };`. For deep cloning, you implement it manually or use a library like `AutoMapper`.

**Q: Where is Prototype used in real .NET applications?**
> `Object.MemberwiseClone()` is the underlying mechanism. EF Core's change tracking keeps prototype snapshots of entities. The `with` keyword on C# records is a built-in language-level prototype.

#### Common Mistakes
- Doing a shallow copy when fields are reference types — the clone shares mutable state with the original.
- Forgetting to clone nested collections — the most common bug.
- Using `ICloneable` from the BCL — it returns `object`, is ambiguous about shallow vs deep, and is considered a poor interface design.

#### Related Patterns
- **Factory Method** — Prototype can replace factory classes when object creation varies only in state, not in type.
- **Memento** — also captures object state, but for the purpose of undo rather than cloning.

#### Key Takeaways
- Prototype = clone an existing object instead of creating from scratch.
- Always implement typed `Clone()` and handle deep copy for reference-type fields.
- In modern C#, `record` types with `with` expressions are a language-native prototype mechanism.

> **Summary — Creational Patterns:** Singleton (one instance), Factory Method (delegate creation to subclass), Abstract Factory (create a family of objects), Builder (step-by-step complex construction), Prototype (clone instead of construct). All five solve the problem of coupling client code to `new ConcreteType()`.

---

# Part 3 — Structural Patterns

> **Category intent:** Assemble objects and classes into larger structures while keeping those structures flexible and efficient.

---

## 6. Adapter

#### Intent
Convert the interface of a class into another interface that clients expect. Adapter lets classes work together that otherwise could not because of **incompatible interfaces**.

#### Problem It Solves
- You have existing code (legacy system, third-party library) with an incompatible interface.
- You cannot modify the existing code (no source, or it would break other consumers).
- You need a bridge between the old interface and the new expected interface.

#### When to Use
- Integrating legacy code or third-party libraries with your system's interface.
- Reusing existing classes when the interface does not match.
- Building a "wrapper" around an external API so your system does not depend on it directly.

#### When NOT to Use
- When you can modify the original class — just change its interface directly.
- When the two interfaces are so different that adaptation creates a confusing, leaky abstraction.

#### Real-World Analogy
A travel power adapter. Your laptop charger has a UK plug; the Indian outlet has a different shape. The adapter physically converts one plug shape to another — the laptop and the wall socket do not change.

#### Enterprise Use Case
Your system uses `ILogger<T>` (Microsoft's logging abstraction), but a legacy library uses a custom `ILegacyLogger` interface. An adapter wraps the `ILogger<T>` and exposes `ILegacyLogger` so the legacy code works without modification.

#### C# Code Example

```csharp
// The interface your system expects
public interface ILogger
{
    void Log(string message);
}

// Legacy class with incompatible interface (cannot modify — third-party)
public class LegacyFileLogger
{
    public void WriteToFile(string timestamp, string content)
        => Console.WriteLine($"[FILE {timestamp}] {content}");
}

// Adapter — wraps LegacyFileLogger, exposes ILogger
public class LegacyLoggerAdapter : ILogger
{
    private readonly LegacyFileLogger _legacyLogger;

    public LegacyLoggerAdapter(LegacyFileLogger legacyLogger)
    {
        _legacyLogger = legacyLogger;
    }

    public void Log(string message)
    {
        // Translate ILogger.Log() → LegacyFileLogger.WriteToFile()
        _legacyLogger.WriteToFile(DateTime.Now.ToString("HH:mm:ss"), message);
    }
}

// Client — only knows ILogger
public class OrderService
{
    private readonly ILogger _logger;
    public OrderService(ILogger logger) => _logger = logger;

    public void PlaceOrder(string item)
    {
        _logger.Log($"Order placed for: {item}"); // works with any ILogger
    }
}

// Usage
var legacy = new LegacyFileLogger();
ILogger adapter = new LegacyLoggerAdapter(legacy);
var service = new OrderService(adapter);
service.PlaceOrder("Laptop"); // [FILE 14:30:00] Order placed for: Laptop
```

#### Step-by-Step Explanation
1. `ILogger` is the interface your system expects.
2. `LegacyFileLogger` has a different method signature — incompatible.
3. `LegacyLoggerAdapter` implements `ILogger` (what the client needs).
4. Internally, it delegates to `LegacyFileLogger.WriteToFile()` — translating the call.
5. `OrderService` is unaware of the legacy system — it only uses `ILogger`.

#### Advantages
- Works with incompatible interfaces without modifying either side.
- Isolates your system from external/legacy code.
- Single Responsibility — adaptation logic is in one place.

#### Disadvantages
- Adds an extra layer of indirection.
- If many methods need adapting, the adapter class becomes large.
- Can hide important differences between the two interfaces.

#### Common Interview Q&A

**Q: What is the difference between Adapter and Facade?**
> Adapter makes two existing, incompatible interfaces work together (interface translation). Facade creates a simplified interface over a complex subsystem (interface simplification). Adapter is about compatibility; Facade is about simplicity. See the comparison section for the full table.

**Q: What are the two types of Adapter?**
> Object Adapter (composition — holds an instance of the adaptee; used in C# since there is no multiple inheritance) and Class Adapter (inheritance — inherits from both the target and adaptee; possible in languages with multiple inheritance, not idiomatic in C#).

**Q: Where is Adapter used in ASP.NET Core?**
> The `ILogger` abstraction itself is an adapter pattern — any logging framework (Serilog, NLog) is wrapped in an adapter that implements `ILogger`. `HttpMessageHandler` adapters in `HttpClient` are another example.

#### Common Mistakes
- Confusing Adapter with Facade — they solve different problems.
- Using Adapter when you could simply refactor — do not add indirection unnecessarily.
- Letting adapter logic grow too large — if you are transforming data and business rules, something else is needed (e.g., an Anti-Corruption Layer in DDD).

#### Related Patterns
- **Facade** — simplifies a subsystem; Adapter translates between interfaces.
- **Decorator** — adds behavior; Adapter changes the interface.
- **Proxy** — same interface as the subject; Adapter changes the interface.

#### Key Takeaways
- Adapter = wrapper that translates one interface into another.
- Used to integrate legacy or third-party code without modifying either side.
- In C#, always use object adapter (composition) over class adapter (inheritance).

---

## 7. Bridge

#### Intent
**Decouple an abstraction from its implementation** so that the two can vary independently.

#### Problem It Solves
- An inheritance hierarchy that grows in two dimensions (e.g., shapes AND rendering methods) creates an exponential class explosion.
- `CircleOnVector`, `CircleOnRaster`, `SquareOnVector`, `SquareOnRaster` — adding a new shape or a new renderer requires many new classes.

#### When to Use
- You have two orthogonal dimensions of variation (e.g., what + how, domain + platform, abstraction + implementation).
- You want to switch implementations at runtime.
- You want to extend both hierarchies independently.

#### When NOT to Use
- Only one dimension varies — ordinary polymorphism is simpler.
- The abstraction and implementation are always coupled — separation adds complexity without benefit.

#### Real-World Analogy
A TV remote (abstraction) and a TV brand (implementation). The remote's interface (volume up, channel change) is separate from the TV's internal hardware. One remote design works with Samsung, LG, or Sony TVs. Add a new remote design or a new TV brand independently.

#### Enterprise Use Case
A notification system where `NotificationType` (Urgent, Normal, Marketing) is the abstraction and `NotificationChannel` (Email, SMS, Push) is the implementation. Any combination works without a class per pair.

#### C# Code Example

```csharp
// Implementation interface
public interface INotificationChannel
{
    void Send(string recipient, string message);
}

// Concrete implementations
public class EmailChannel : INotificationChannel
{
    public void Send(string recipient, string message)
        => Console.WriteLine($"Email to {recipient}: {message}");
}

public class SmsChannel : INotificationChannel
{
    public void Send(string recipient, string message)
        => Console.WriteLine($"SMS to {recipient}: {message}");
}

// Abstraction — holds a reference to the implementation (the bridge)
public abstract class Notification
{
    protected INotificationChannel _channel;
    public Notification(INotificationChannel channel) => _channel = channel;
    public abstract void Notify(string recipient, string message);
}

// Refined abstractions
public class UrgentNotification : Notification
{
    public UrgentNotification(INotificationChannel channel) : base(channel) {}
    public override void Notify(string recipient, string message)
        => _channel.Send(recipient, $"[URGENT] {message}");
}

public class MarketingNotification : Notification
{
    public MarketingNotification(INotificationChannel channel) : base(channel) {}
    public override void Notify(string recipient, string message)
        => _channel.Send(recipient, $"[PROMO] {message}");
}

// Usage — mix and match abstraction + implementation independently
var urgentSms   = new UrgentNotification(new SmsChannel());
var marketEmail = new MarketingNotification(new EmailChannel());

urgentSms.Notify("Alice", "Server is down!");
marketEmail.Notify("Bob", "50% off today!");
```

#### Step-by-Step Explanation
1. `INotificationChannel` is the implementation interface — defines how a message is sent.
2. `EmailChannel` and `SmsChannel` are concrete implementations.
3. `Notification` is the abstraction — it holds a `_channel` (the bridge to the implementation).
4. `UrgentNotification` and `MarketingNotification` are refined abstractions — they add type-specific behavior.
5. Any abstraction works with any implementation — 2 abstractions × 2 implementations = 4 combinations from 4 classes, not 4 classes.

#### Advantages
- Eliminates class explosion in multi-dimensional hierarchies.
- Abstraction and implementation can evolve independently.
- Follows OCP and SRP.

#### Disadvantages
- More complex setup than simple inheritance.
- Can be overkill if only one dimension varies.

#### Common Interview Q&A

**Q: What is the class explosion problem and how does Bridge solve it?**
> Without Bridge, N shapes × M renderers = N×M classes. With Bridge, it is N + M classes — one per shape and one per renderer, composed at runtime. For 5 shapes and 4 renderers: 20 classes vs 9 classes.

**Q: What is the difference between Bridge and Strategy?**
> Bridge separates an abstraction from its implementation and both can vary. Strategy replaces an algorithm within a single object. Bridge is a structural pattern about organization; Strategy is a behavioral pattern about behavior selection.

#### Common Mistakes
- Confusing Bridge with Adapter — Adapter makes incompatible interfaces work; Bridge is designed upfront to separate two dimensions.
- Applying Bridge when only one dimension varies — unnecessary complexity.

#### Related Patterns
- **Adapter** — Adapter is for compatibility; Bridge is for designed separation.
- **Strategy** — Strategy bridges behavior; Bridge bridges entire implementations.

#### Key Takeaways
- Bridge = separate abstraction from implementation so both vary independently.
- Eliminates NxM class explosion with N+M classes instead.
- Compose the two dimensions at runtime via constructor injection.

---

## 8. Composite

#### Intent
Compose objects into **tree structures to represent part-whole hierarchies**. Lets clients treat individual objects and compositions of objects uniformly.

#### Problem It Solves
- Code must handle both individual items and groups of items differently — `if (isGroup) { ... } else { ... }`.
- Tree structures (file systems, UI component trees, org charts, menus) need uniform traversal.

#### When to Use
- Representing part-whole hierarchies (trees).
- Clients should be able to treat leaf nodes and composite nodes identically.
- Building recursive structures (menus with sub-menus, folders with sub-folders).

#### When NOT to Use
- When the hierarchy is flat or never nested — adds unnecessary complexity.
- When leaf and composite nodes truly need very different interfaces.

#### Real-World Analogy
A company org chart. A `Manager` is a node that contains `Employee` nodes. You can call `GetSalary()` on a single employee, or on a manager (which sums up all employees under them). The client code calls `GetSalary()` the same way regardless.

#### Enterprise Use Case
A UI rendering engine. A `Panel` contains `Button`, `TextBox`, and other `Panel` objects. Calling `Render()` on the root panel recursively renders the entire UI tree.

#### C# Code Example

```csharp
// Component interface — uniform interface for both leaf and composite
public abstract class FileSystemItem
{
    public string Name { get; }
    public FileSystemItem(string name) => Name = name;
    public abstract long GetSize();
    public abstract void Display(int indent = 0);
}

// Leaf — no children
public class File : FileSystemItem
{
    private long _size;
    public File(string name, long size) : base(name) => _size = size;
    public override long GetSize() => _size;
    public override void Display(int indent = 0)
        => Console.WriteLine($"{new string(' ', indent)}- {Name} ({_size} bytes)");
}

// Composite — has children, delegates to them
public class Folder : FileSystemItem
{
    private List<FileSystemItem> _children = new();

    public Folder(string name) : base(name) {}

    public void Add(FileSystemItem item) => _children.Add(item);

    public override long GetSize() => _children.Sum(c => c.GetSize()); // recursive

    public override void Display(int indent = 0)
    {
        Console.WriteLine($"{new string(' ', indent)}[{Name}]");
        foreach (var child in _children)
            child.Display(indent + 2); // recursive
    }
}

// Usage — client treats File and Folder the same way
var root = new Folder("C:");
var documents = new Folder("Documents");
documents.Add(new File("resume.pdf", 1024));
documents.Add(new File("cover.docx", 512));
root.Add(documents);
root.Add(new File("readme.txt", 128));

root.Display();
Console.WriteLine($"Total size: {root.GetSize()} bytes");
```

#### Step-by-Step Explanation
1. `FileSystemItem` is the component — both `File` and `Folder` extend it.
2. `File` is the leaf — `GetSize()` returns its own size directly.
3. `Folder` is the composite — `GetSize()` sums children recursively; `Display()` delegates to each child.
4. The client calls `Display()` and `GetSize()` on the root — it does not need to know if a node is a `File` or `Folder`.
5. Nesting works to any depth — the tree structure handles it recursively.

#### Advantages
- Uniform treatment of individual items and collections.
- Easy to add new component types (new leaf or composite).
- Recursive operations become natural.

#### Disadvantages
- Hard to restrict what types can be in the composite (you might add a `File` to something that should only contain `Folders`).
- Can make the design overly general.

#### Common Interview Q&A

**Q: What problem does Composite solve over a simple list?**
> A list is flat. Composite handles arbitrary nesting depth. The key is the uniform interface — the same method works on a leaf or a tree of any depth without the client knowing.

**Q: Where is Composite used in .NET?**
> `System.Windows.Controls.Panel` (WPF), `IComposite` UI trees in Blazor, `Expression<T>` trees in LINQ, and menu structures in web frameworks all use the Composite pattern.

#### Common Mistakes
- Not defining the component interface carefully — if leaf and composite interfaces diverge, uniformity breaks.
- Making `Add()`/`Remove()` part of the component interface — leaves then need to throw `NotSupportedException`, which violates LSP.

#### Related Patterns
- **Decorator** — also uses recursive composition, but adds behavior rather than representing hierarchy.
- **Iterator** — used to traverse composite structures.
- **Visitor** — used to add operations to composite structures without modifying them.

#### Key Takeaways
- Composite = tree structure where leaves and branches share a uniform interface.
- Recursive delegation is the core mechanism — composites call the same method on children.
- Best for hierarchical data: file systems, UI trees, org charts, menus.

---

## 9. Decorator

#### Intent
**Attach additional responsibilities to an object dynamically**, providing a flexible alternative to subclassing for extending functionality.

#### Problem It Solves
- You need to add behavior to individual objects, not to the entire class.
- The number of possible combinations of behaviors makes subclassing impractical (e.g., logging + caching + compression in any combination).
- The additional behavior should be composable and removable at runtime.

#### When to Use
- Adding cross-cutting concerns: logging, caching, retry, validation, timing.
- You need to combine behaviors in different combinations dynamically.
- Extending a class you do not own (third-party or sealed).

#### When NOT to Use
- When the order of decoration matters but is not obvious — becomes confusing.
- When you only need one fixed behavior extension — just subclass.
- When many decorators are stacked deep — debugging becomes difficult.

#### Real-World Analogy
A coffee order. Start with a basic espresso. Wrap it in a "milk" decorator. Wrap that in a "sugar" decorator. Wrap that in a "caramel" decorator. Each layer adds to the cost and description. You can combine additions in any order.

#### Enterprise Use Case
A data repository with optional caching and logging. `CachingRepository` wraps `SqlRepository` and adds caching. `LoggingRepository` wraps that and adds logging. Swap out or reorder layers without changing either the repository or the consuming service.

#### C# Code Example

```csharp
// Component interface
public interface IDataRepository
{
    string GetData(int id);
}

// Concrete component
public class SqlDataRepository : IDataRepository
{
    public string GetData(int id)
    {
        Console.WriteLine($"[SQL] Fetching id={id} from database...");
        return $"Data-{id}";
    }
}

// Base decorator — holds a reference to the wrapped component
public abstract class RepositoryDecorator : IDataRepository
{
    protected readonly IDataRepository _inner;
    protected RepositoryDecorator(IDataRepository inner) => _inner = inner;
    public abstract string GetData(int id);
}

// Caching decorator
public class CachingRepository : RepositoryDecorator
{
    private readonly Dictionary<int, string> _cache = new();
    public CachingRepository(IDataRepository inner) : base(inner) {}

    public override string GetData(int id)
    {
        if (_cache.TryGetValue(id, out var cached))
        {
            Console.WriteLine($"[CACHE] Cache hit for id={id}");
            return cached;
        }
        var result = _inner.GetData(id);
        _cache[id] = result;
        return result;
    }
}

// Logging decorator
public class LoggingRepository : RepositoryDecorator
{
    public LoggingRepository(IDataRepository inner) : base(inner) {}

    public override string GetData(int id)
    {
        Console.WriteLine($"[LOG] GetData called with id={id}");
        var result = _inner.GetData(id);
        Console.WriteLine($"[LOG] GetData returned: {result}");
        return result;
    }
}

// Usage — stack decorators in any order
IDataRepository repo = new SqlDataRepository();
repo = new CachingRepository(repo);   // wrap with caching
repo = new LoggingRepository(repo);   // wrap with logging

repo.GetData(1); // [LOG] → [CACHE miss] → [SQL]
repo.GetData(1); // [LOG] → [CACHE hit]
```

#### Step-by-Step Explanation
1. `IDataRepository` is the component interface — all layers implement it.
2. `SqlDataRepository` is the base — the real implementation.
3. `RepositoryDecorator` is the abstract base decorator — holds `_inner` (the wrapped object).
4. `CachingRepository` checks its cache first; on miss, delegates to `_inner`.
5. `LoggingRepository` logs before/after and delegates to `_inner`.
6. Stacking: Logging wraps Caching wraps SQL — each call passes through all layers.

#### Advantages
- Add/remove behavior at runtime without changing the component.
- Combine behaviors in any order and combination.
- Follows OCP and SRP — each decorator has one responsibility.

#### Disadvantages
- Many small classes when many decorators exist.
- Order of decoration is implicit — can cause bugs.
- Debugging through many layers is harder.

#### Common Interview Q&A

**Q: What is the difference between Decorator and Inheritance for extending behavior?**
> Inheritance adds behavior statically at compile time to all instances of a class. Decorator adds behavior dynamically at runtime to individual instances. Decorator is more flexible and avoids subclass explosion.

**Q: Where is Decorator used in ASP.NET Core?**
> ASP.NET Core middleware is a Decorator chain — each middleware wraps the next. `Stream` decorators in .NET (`GZipStream` wraps `FileStream`). `ILogger` with log level filtering is a decorator.

**Q: What is the difference between Decorator and Proxy?**
> Both wrap an object implementing the same interface. Decorator adds behavior. Proxy controls access (lazy loading, caching, authentication). The intent differs — see the comparison section.

#### Common Mistakes
- Not having a base decorator class — repeating `_inner` delegation in every concrete decorator.
- Decorating in the wrong order — caching before validation means invalid requests get cached.
- Confusing with Proxy — Proxy is about access control; Decorator is about adding behavior.

#### Related Patterns
- **Proxy** — similar structure; different intent.
- **Composite** — also uses recursive wrapping.
- **Strategy** — Strategy replaces behavior; Decorator adds behavior.

#### Key Takeaways
- Decorator = wrap an object to add behavior, preserving the interface.
- Stack multiple decorators for cross-cutting concerns: logging, caching, retry.
- ASP.NET Core middleware is the Decorator pattern in action.

> **Summary — Structural Patterns (first half):** Adapter (translate interfaces), Bridge (separate abstraction from implementation), Composite (part-whole tree hierarchy), Decorator (add behavior dynamically by wrapping). All use composition to build flexible structures.

---

## 10. Facade

#### Intent
Provide a **simplified, unified interface to a complex subsystem** of classes.

#### Problem It Solves
- Clients interact with many complex classes in the right sequence — tight coupling to subsystem internals.
- A subsystem has a steep learning curve — clients must understand many classes to accomplish one task.
- Changes inside the subsystem ripple out to many callers.

#### When to Use
- Providing a simple entry point to a complex library or subsystem.
- Layering your architecture — the Facade defines a layer boundary.
- Reducing dependencies between the client and the subsystem.

#### When NOT to Use
- When clients genuinely need fine-grained control over the subsystem — the facade would hide necessary details.
- When the "simplification" becomes a bottleneck that all code must pass through.

#### Real-World Analogy
A hotel concierge. You say "arrange a birthday dinner for 8 tonight." The concierge calls the restaurant, the florist, the cake shop, and the driver. You interact with one person; the concierge orchestrates the entire subsystem.

#### Enterprise Use Case
An `OrderFacade` in an e-commerce system. Placing an order involves: checking inventory, processing payment, creating a shipment, sending a confirmation email. The facade coordinates all subsystems — client code calls `PlaceOrder()`.

#### C# Code Example

```csharp
// Complex subsystems
public class InventoryService
{
    public bool CheckStock(int productId) { Console.WriteLine("Checking inventory..."); return true; }
}

public class PaymentService
{
    public bool Charge(string card, decimal amount) { Console.WriteLine("Charging card..."); return true; }
}

public class ShippingService
{
    public string CreateShipment(int productId) { Console.WriteLine("Creating shipment..."); return "SHIP-001"; }
}

public class EmailService
{
    public void SendConfirmation(string email, string trackingCode)
        => Console.WriteLine($"Email sent to {email}, tracking: {trackingCode}");
}

// Facade — simple interface over all four subsystems
public class OrderFacade
{
    private readonly InventoryService _inventory = new();
    private readonly PaymentService   _payment   = new();
    private readonly ShippingService  _shipping  = new();
    private readonly EmailService     _email     = new();

    public bool PlaceOrder(int productId, string card, decimal amount, string email)
    {
        if (!_inventory.CheckStock(productId)) return false;
        if (!_payment.Charge(card, amount))    return false;
        var tracking = _shipping.CreateShipment(productId);
        _email.SendConfirmation(email, tracking);
        Console.WriteLine("Order placed successfully.");
        return true;
    }
}

// Usage — client only knows OrderFacade
var facade = new OrderFacade();
facade.PlaceOrder(42, "4111-1111-1111-1111", 99.99m, "alice@example.com");
```

#### Step-by-Step Explanation
1. Four subsystem classes each handle one concern.
2. `OrderFacade` owns instances of all four subsystems.
3. `PlaceOrder()` orchestrates the correct sequence: check → charge → ship → notify.
4. Client code calls one method — it does not know about the four subsystems.
5. If the subsystem changes (e.g., shipping uses a new API), only the facade changes.

#### Advantages
- Dramatically simplifies client code.
- Decouples clients from subsystem internals.
- Creates a clear layer boundary in your architecture.
- Easy to change subsystem implementation without affecting clients.

#### Disadvantages
- Can become a "god object" that does too much.
- Hides subsystem capabilities — clients with special needs must bypass the facade.
- Does not prevent direct subsystem access — discipline needed to enforce the boundary.

#### Common Interview Q&A

**Q: What is the difference between Facade and Adapter?**
> Adapter makes two incompatible interfaces work together. Facade creates a new, simpler interface over a complex system. Adapter is about translation; Facade is about simplification.

**Q: How is Facade different from just a Service class?**
> They are similar in practice. Facade is the pattern name for a service class that wraps multiple subsystems behind a simple interface. In practice, ASP.NET Core service classes often ARE facades.

**Q: Does Facade hide the subsystem?**
> It simplifies access to the subsystem but does not prevent direct access. It is a design convention, not a hard barrier. Some architectures enforce the boundary with project/assembly separation.

#### Common Mistakes
- Turning the Facade into a God Object with unrelated responsibilities.
- Exposing too much — the facade should only include what clients genuinely need.
- Hiding critical error handling — the facade must handle subsystem failures gracefully.

#### Related Patterns
- **Adapter** — compatibility; Facade — simplification.
- **Mediator** — also centralizes coordination, but Mediator removes direct coupling between all colleagues; Facade typically still owns its subsystems.

#### Key Takeaways
- Facade = simple interface to a complex subsystem.
- Reduces coupling and provides an architectural layer boundary.
- Service classes in ASP.NET Core applications ARE facades in practice.

---

## 11. Flyweight

#### Intent
Use **sharing to efficiently support a large number of fine-grained objects** by externalizing their state.

#### Problem It Solves
- You need a huge number of similar objects (thousands or millions) that consume too much memory.
- Most of the object's state is identical across instances — storing it per-object is wasteful.

#### When to Use
- Applications with very large numbers of similar objects (game characters, text rendering, particles).
- Memory is the primary concern.
- Most object state can be made extrinsic (passed in by the caller, not stored in the object).

#### When NOT to Use
- When the number of objects is small — the complexity is not worth it.
- When there is no clear intrinsic (shared) vs extrinsic (unique) state split.

#### Real-World Analogy
A text editor rendering characters. Instead of one full object per character (font, size, color, position, the character itself), the flyweight stores the shared intrinsic state (font, size, color) once and receives the extrinsic state (position, character value) at render time.

#### Enterprise Use Case
A multiplayer game with 10,000 tree objects on a map. Each tree shares the same texture, model, and color (intrinsic). Only position (extrinsic) differs per tree instance.

#### C# Code Example

```csharp
// Flyweight — stores only intrinsic (shared) state
public class TreeType
{
    public string Name    { get; }
    public string Color   { get; }
    public string Texture { get; }

    public TreeType(string name, string color, string texture)
    {
        Name = name; Color = color; Texture = texture;
    }

    public void Draw(int x, int y) // extrinsic state passed in
        => Console.WriteLine($"Drawing {Name} ({Color}) at ({x},{y}) with texture '{Texture}'");
}

// Flyweight Factory — ensures shared instances
public class TreeTypeFactory
{
    private static readonly Dictionary<string, TreeType> _cache = new();

    public static TreeType GetTreeType(string name, string color, string texture)
    {
        var key = $"{name}_{color}_{texture}";
        if (!_cache.ContainsKey(key))
        {
            Console.WriteLine($"[FACTORY] Creating new TreeType: {name}");
            _cache[key] = new TreeType(name, color, texture);
        }
        return _cache[key]; // return shared instance
    }
}

// Context — holds extrinsic state + reference to shared flyweight
public class Tree
{
    private int _x, _y;
    private TreeType _type; // shared

    public Tree(int x, int y, TreeType type) { _x = x; _y = y; _type = type; }
    public void Draw() => _type.Draw(_x, _y);
}

// Usage — thousands of Trees, only 2 TreeType objects in memory
var forest = new List<Tree>();
for (int i = 0; i < 5; i++)
{
    var type = TreeTypeFactory.GetTreeType("Oak", "Green", "oak_texture.png");
    forest.Add(new Tree(i * 10, i * 5, type));
}
forest.ForEach(t => t.Draw());
// [FACTORY] Creating new TreeType: Oak  — only once!
```

#### Step-by-Step Explanation
1. `TreeType` stores intrinsic state — data shared across all oak trees.
2. `TreeTypeFactory` ensures only one `TreeType` per unique type is ever created.
3. `Tree` is the context — stores extrinsic state (position) + a reference to the shared flyweight.
4. 5000 `Tree` objects exist but only 1 `TreeType` object — massive memory saving.
5. `Draw()` combines the shared intrinsic state with the per-tree extrinsic state.

#### Advantages
- Dramatic memory reduction when many similar objects exist.
- Centralized intrinsic state management.

#### Disadvantages
- Trades memory for CPU — extrinsic state must be computed/passed at every call.
- Code complexity increases — intrinsic/extrinsic split must be carefully designed.
- Shared state must be immutable — mutable shared state causes bugs.

#### Common Interview Q&A

**Q: What is intrinsic vs extrinsic state?**
> Intrinsic state is shared across instances and stored in the flyweight (e.g., font name, texture file). Extrinsic state is unique per context and passed to the flyweight at operation time (e.g., character position, tree coordinates).

**Q: Where is Flyweight used in .NET?**
> `string` interning in C# is flyweight — equal string literals share the same memory object. `Char` structs, cached `Task.FromResult()` values, and compiled regex patterns use flyweight-like sharing.

#### Common Mistakes
- Making intrinsic state mutable — all sharers of the flyweight would see the change.
- Including extrinsic state in the flyweight — breaks sharing.

#### Related Patterns
- **Singleton** — the factory method often produces Singleton flyweight instances.
- **Composite** — flyweights are often used as leaf nodes in composite structures.

#### Key Takeaways
- Flyweight = share intrinsic state; pass extrinsic state at runtime.
- Massive memory savings for large numbers of similar objects.
- Shared flyweight state must always be immutable.

---

## 12. Proxy

#### Intent
Provide a **surrogate or placeholder for another object** to control access to it.

#### Problem It Solves
- Direct access to an object is costly (remote, slow to create, or expensive).
- You need to add access control, logging, or lazy initialization without changing the real subject.
- The real object should only be created or accessed under certain conditions.

#### When to Use
- **Virtual proxy:** Lazy initialization of expensive objects.
- **Protection proxy:** Access control — check permissions before delegating.
- **Remote proxy:** Represent an object that lives in another process or machine.
- **Caching proxy:** Cache results of expensive operations.
- **Logging proxy:** Log all calls to the real object.

#### When NOT to Use
- When access is always allowed and no pre/post processing is needed — just use the real object.
- Simple objects where the proxy adds more complexity than value.

#### Real-World Analogy
A security guard at a building entrance. You present credentials to the guard (proxy). The guard checks them. Only then do you get access to the building (real subject). The guard controls access without changing anything about the building.

#### Enterprise Use Case
A lazy-loading proxy for an `IExpensiveReportService` — the service only connects to the reporting database when `Generate()` is actually called, not when the proxy is created.

#### C# Code Example

```csharp
// Subject interface
public interface IReportService
{
    string Generate(string reportName);
}

// Real subject — expensive to create (connects to DB, loads data)
public class ReportService : IReportService
{
    public ReportService()
        => Console.WriteLine("[ReportService] Connected to reporting DB (expensive!)");

    public string Generate(string reportName)
    {
        Console.WriteLine($"[ReportService] Generating: {reportName}");
        return $"Report: {reportName}";
    }
}

// Virtual (lazy-loading) proxy
public class LazyReportServiceProxy : IReportService
{
    private ReportService? _real = null; // not created yet

    private ReportService GetReal()
    {
        if (_real == null)
        {
            Console.WriteLine("[Proxy] First access — creating real service...");
            _real = new ReportService(); // created only on first use
        }
        return _real;
    }

    public string Generate(string reportName)
        => GetReal().Generate(reportName);
}

// Protection proxy
public class SecureReportProxy : IReportService
{
    private readonly IReportService _inner;
    private readonly string _userRole;

    public SecureReportProxy(IReportService inner, string userRole)
    {
        _inner = inner; _userRole = userRole;
    }

    public string Generate(string reportName)
    {
        if (_userRole != "Admin")
            throw new UnauthorizedAccessException("Only admins can generate reports.");
        return _inner.Generate(reportName);
    }
}

// Usage
IReportService proxy = new LazyReportServiceProxy();
Console.WriteLine("Proxy created — no DB connection yet.");
proxy.Generate("SalesReport"); // now the real service is created
```

#### Step-by-Step Explanation
1. `IReportService` is the shared interface for both proxy and real subject.
2. `ReportService` is the real subject — expensive constructor simulates DB connection.
3. `LazyReportServiceProxy` delays creation of `ReportService` until `Generate()` is first called.
4. `SecureReportProxy` wraps any `IReportService` and checks role before delegating.
5. Client code uses `IReportService` — it cannot tell whether it has the real service or a proxy.

#### Advantages
- Control access without changing the real subject.
- Lazy initialization reduces startup cost.
- Add cross-cutting concerns (security, logging, caching) transparently.

#### Disadvantages
- Response time may increase due to indirection.
- Complex chains of proxies are hard to debug.

#### Common Interview Q&A

**Q: What is the difference between Proxy and Decorator?**
> Both wrap an object with the same interface. Proxy controls access to the object (lazy loading, authentication, remote access). Decorator adds new behavior to the object. Proxy is about access; Decorator is about enhancement.

**Q: What are the types of proxy?**
> Virtual (lazy init), Protection (access control), Remote (represent remote object), Caching (cache results), Logging (audit calls), Smart Reference (additional actions on access like reference counting).

**Q: Where is Proxy used in .NET?**
> Entity Framework lazy loading uses a virtual proxy — related entities are not loaded until accessed. Castle Windsor's `DynamicProxy` generates proxy classes at runtime for AOP (Aspect-Oriented Programming). `HttpClient` with handlers is a proxy chain.

#### Common Mistakes
- Using Proxy when Decorator is appropriate and vice versa — understand the intent difference.
- Forgetting to implement all interface methods in the proxy.
- Creating expensive resources in the proxy constructor — defeats the purpose of a virtual proxy.

#### Related Patterns
- **Decorator** — adds behavior; Proxy controls access.
- **Adapter** — changes interface; Proxy keeps the same interface.
- **Facade** — simplifies interface; Proxy keeps the same interface.

#### Key Takeaways
- Proxy = same interface as the real object, controls access to it.
- Types: Virtual (lazy), Protection (auth), Remote, Caching, Logging.
- EF Core lazy loading is a classic real-world proxy in .NET.

> **Summary — Structural Patterns (second half):** Facade (simple interface to complex subsystem), Flyweight (share intrinsic state across many objects), Proxy (control access to an object). All three add a layer of indirection for different reasons: simplicity, memory, and access control respectively.

---

# Part 4 — Behavioral Patterns

> **Category intent:** Define how objects communicate, share responsibility, and coordinate behavior.

---

## 13. Chain of Responsibility

#### Intent
Pass a request along a **chain of handlers** where each handler decides either to process the request or pass it to the next handler.

#### Problem It Solves
- Multiple objects may handle a request, but which one is not known at design time.
- You want to decouple the sender from the receiver.
- The set of handlers and their order should be configurable at runtime.

#### When to Use
- Processing pipelines: middleware, request validation, approval workflows.
- When more than one object may handle a request.
- When you want to issue a request without specifying the receiver explicitly.

#### When NOT to Use
- When a request must always be handled — chain can accidentally drop a request.
- When only one handler will ever handle the request — use a simple strategy instead.

#### Real-World Analogy
A help desk escalation chain. You call tier-1 support. They cannot solve it → escalate to tier-2. Cannot solve it → escalate to tier-3. Each level handles what it can; the rest passes up.

#### Enterprise Use Case
An expense approval workflow. Expenses under $1,000 are approved by a Team Lead. Under $10,000 by a Manager. Under $100,000 by a Director. Over that, by the CEO. Each handler checks if it can approve; otherwise passes to the next.

#### C# Code Example

```csharp
// Handler interface
public abstract class ExpenseHandler
{
    protected ExpenseHandler? _next;

    public ExpenseHandler SetNext(ExpenseHandler next)
    {
        _next = next;
        return next; // allows chaining: lead.SetNext(manager).SetNext(director)
    }

    public abstract void Handle(decimal amount);
}

// Concrete handlers
public class TeamLeadHandler : ExpenseHandler
{
    public override void Handle(decimal amount)
    {
        if (amount <= 1000) Console.WriteLine($"TeamLead approved ${amount}");
        else _next?.Handle(amount);
    }
}

public class ManagerHandler : ExpenseHandler
{
    public override void Handle(decimal amount)
    {
        if (amount <= 10000) Console.WriteLine($"Manager approved ${amount}");
        else _next?.Handle(amount);
    }
}

public class DirectorHandler : ExpenseHandler
{
    public override void Handle(decimal amount)
    {
        if (amount <= 100000) Console.WriteLine($"Director approved ${amount}");
        else Console.WriteLine($"${amount} requires Board approval — escalated.");
    }
}

// Usage — build the chain
var lead    = new TeamLeadHandler();
var manager = new ManagerHandler();
var director = new DirectorHandler();
lead.SetNext(manager).SetNext(director);

lead.Handle(500);    // TeamLead approved
lead.Handle(8000);   // Manager approved
lead.Handle(50000);  // Director approved
lead.Handle(200000); // Board escalation
```

#### Step-by-Step Explanation
1. `ExpenseHandler` defines the chain link — holds a `_next` reference and `Handle()`.
2. `SetNext()` links handlers and returns `next` to allow fluent chaining.
3. Each handler checks if it can handle the amount. If yes, it acts. If no, it passes to `_next`.
4. The chain is built by the client — the order is explicit and configurable.
5. A request automatically travels through the chain until handled or the end is reached.

#### Advantages
- Decouples sender from receivers.
- Chain can be assembled and modified at runtime.
- Each handler has one responsibility.

#### Disadvantages
- Requests can go unhandled if no handler in the chain processes them.
- Debugging a long chain is harder — not obvious which handler acted.
- Performance cost if the chain is very long.

#### Common Interview Q&A

**Q: How does ASP.NET Core middleware relate to Chain of Responsibility?**
> ASP.NET Core middleware is exactly Chain of Responsibility. Each middleware calls `await _next(context)` to pass the request down the chain. Middleware like authentication, routing, CORS, and error handling are all handlers in the pipeline.

**Q: What happens if no handler processes the request?**
> It depends on the implementation. In the example above, the last handler can either handle all remaining cases or log/throw. ASP.NET Core returns a 404 if no middleware handles the request.

#### Common Mistakes
- Forgetting to call `_next?.Handle()` — silently drops requests.
- Circular chains — lead → manager → lead — cause infinite loops.
- Hard-coding the chain — defeats the purpose of runtime configurability.

#### Related Patterns
- **Decorator** — also chains, but every decorator processes; CoR can stop the chain.
- **Command** — often combined with CoR to queue commands through a pipeline.

#### Key Takeaways
- Chain of Responsibility = pipeline of handlers; each decides to handle or pass on.
- ASP.NET Core middleware IS this pattern in action.
- Always handle the "no handler matched" case at the end of the chain.

---

## 14. Command

#### Intent
Encapsulate a request as an **object**, allowing you to parameterize clients, queue requests, log them, and support undoable operations.

#### Problem It Solves
- Tight coupling between the sender of a request and the object that performs it.
- You need to support undo/redo, queuing, or logging of operations.
- You need to execute operations at a different time than when they are issued.

#### When to Use
- Undo/redo functionality (text editors, drawing apps).
- Task queues and job scheduling.
- Transactional operations that can be rolled back.
- GUI actions (button clicks mapped to commands).

#### When NOT to Use
- Simple method calls with no need for queueing, logging, or undoing.
- When the overhead of command objects outweighs the benefits.

#### Real-World Analogy
A restaurant order. The waiter (invoker) writes the order on a slip (command object) and hands it to the kitchen (receiver). The waiter does not cook — they relay the command. The slip can be queued, re-executed, or cancelled.

#### Enterprise Use Case
A bank transaction system where every `Transfer`, `Deposit`, and `Withdrawal` is a command object. They can be executed, queued for batch processing, logged for audit, and rolled back if a transaction fails.

#### C# Code Example

```csharp
// Command interface
public interface ICommand
{
    void Execute();
    void Undo();
}

// Receiver — the object that does the actual work
public class BankAccount
{
    public decimal Balance { get; private set; } = 1000;
    public void Deposit(decimal amount)    { Balance += amount; Console.WriteLine($"Deposited ${amount}. Balance: ${Balance}"); }
    public void Withdraw(decimal amount)   { Balance -= amount; Console.WriteLine($"Withdrew ${amount}. Balance: ${Balance}"); }
}

// Concrete command
public class DepositCommand : ICommand
{
    private readonly BankAccount _account;
    private readonly decimal _amount;

    public DepositCommand(BankAccount account, decimal amount)
    {
        _account = account; _amount = amount;
    }

    public void Execute() => _account.Deposit(_amount);
    public void Undo()    => _account.Withdraw(_amount); // reverse the action
}

// Invoker — stores and executes commands
public class TransactionManager
{
    private readonly Stack<ICommand> _history = new();

    public void Execute(ICommand command)
    {
        command.Execute();
        _history.Push(command);
    }

    public void UndoLast()
    {
        if (_history.TryPop(out var cmd))
            cmd.Undo();
    }
}

// Usage
var account = new BankAccount();
var manager = new TransactionManager();

manager.Execute(new DepositCommand(account, 500));  // Balance: $1500
manager.Execute(new DepositCommand(account, 200));  // Balance: $1700
manager.UndoLast();                                 // Undo deposit of $200 → $1500
```

#### Step-by-Step Explanation
1. `ICommand` defines `Execute()` and `Undo()` — the command contract.
2. `BankAccount` is the receiver — it has the real business logic.
3. `DepositCommand` encapsulates one operation: which account, how much, and how to reverse it.
4. `TransactionManager` is the invoker — it executes commands and maintains a history stack.
5. `UndoLast()` pops the last command and calls `Undo()` — reverses the operation.

#### Advantages
- Decouples invoker from receiver.
- Built-in undo/redo support via command history.
- Commands can be queued, scheduled, and logged.
- Macro commands (composite commands) are easy to add.

#### Disadvantages
- Many small command classes for complex systems.
- Undo logic can be complex to implement correctly.
- Stateful commands hold references to receivers — memory management needed.

#### Common Interview Q&A

**Q: What is MediatR and how does it relate to the Command pattern?**
> MediatR is a popular .NET library that implements the Command and Mediator patterns together. You define command classes (e.g., `PlaceOrderCommand`) and handlers (`PlaceOrderHandler`). MediatR routes the command to its handler — decoupling the sender from the handler completely.

**Q: How do you implement a macro command (composite command)?**
> Create a `MacroCommand : ICommand` that holds a `List<ICommand>`. `Execute()` calls `Execute()` on each. `Undo()` calls `Undo()` on each in reverse order.

#### Common Mistakes
- Forgetting to implement `Undo()` properly — reverse logic must mirror `Execute()` exactly.
- Storing too much state in commands — leads to memory leaks in long-running systems.
- Not clearing command history — the history stack grows unbounded.

#### Related Patterns
- **Memento** — used to capture state for undo; Command records what action to undo.
- **Strategy** — both encapsulate behavior, but Strategy is a choice of algorithm; Command is a request object.
- **Chain of Responsibility** — commands can be passed through a chain.

#### Key Takeaways
- Command = encapsulate a request as an object for queuing, logging, and undo.
- MediatR in ASP.NET Core is the Command pattern in modern .NET.
- Always implement `Undo()` when you need rollback or history.

---

## 15. Interpreter

#### Intent
Define a representation for a language's grammar and provide an **interpreter to deal with that grammar**.

#### Problem It Solves
- You have a simple language or expression syntax to evaluate (math expressions, SQL subsets, configuration DSLs).
- You need to parse and evaluate rules at runtime without hard-coding them.

#### When to Use
- The grammar is simple and stable.
- Efficiency is not critical.
- The language can be represented as an abstract syntax tree (AST).

#### When NOT to Use
- Complex grammars — use a dedicated parser generator (ANTLR, Roslyn) instead.
- Performance-critical scenarios — the interpreter pattern has overhead.

#### Real-World Analogy
A musical score is a language. A musician is the interpreter. Each note, rest, and tempo marking is a grammar rule. The musician reads the score and produces sound — interpreting the language.

#### Enterprise Use Case
A rule engine that evaluates discount eligibility expressions: `"PremiumCustomer AND OrderTotal > 500"`. Each token is an expression object; the interpreter evaluates the tree.

#### C# Code Example

```csharp
// Abstract expression
public interface IExpression
{
    bool Interpret(Dictionary<string, bool> context);
}

// Terminal expressions
public class VariableExpression : IExpression
{
    private readonly string _name;
    public VariableExpression(string name) => _name = name;
    public bool Interpret(Dictionary<string, bool> context)
        => context.TryGetValue(_name, out var val) && val;
}

// Non-terminal (composite) expressions
public class AndExpression : IExpression
{
    private readonly IExpression _left, _right;
    public AndExpression(IExpression left, IExpression right) { _left = left; _right = right; }
    public bool Interpret(Dictionary<string, bool> context)
        => _left.Interpret(context) && _right.Interpret(context);
}

public class OrExpression : IExpression
{
    private readonly IExpression _left, _right;
    public OrExpression(IExpression left, IExpression right) { _left = left; _right = right; }
    public bool Interpret(Dictionary<string, bool> context)
        => _left.Interpret(context) || _right.Interpret(context);
}

// Usage — build expression: IsPremium AND (HasCoupon OR IsVIP)
var isPremium = new VariableExpression("IsPremium");
var hasCoupon = new VariableExpression("HasCoupon");
var isVip     = new VariableExpression("IsVIP");
var rule = new AndExpression(isPremium, new OrExpression(hasCoupon, isVip));

var context = new Dictionary<string, bool>
{
    ["IsPremium"] = true,
    ["HasCoupon"] = false,
    ["IsVIP"]     = true
};

Console.WriteLine(rule.Interpret(context)); // True
```

#### Step-by-Step Explanation
1. `IExpression` is the grammar rule interface.
2. `VariableExpression` is a terminal — it looks up a boolean value in the context.
3. `AndExpression` and `OrExpression` are non-terminals — they compose two sub-expressions.
4. The expression tree `IsPremium AND (HasCoupon OR IsVIP)` is built by composing objects.
5. `Interpret(context)` recursively evaluates the tree against the runtime context.

#### Advantages
- Adding new grammar rules is easy — add a new `IExpression` class.
- Expressions are composable — complex rules built from simple pieces.

#### Disadvantages
- Complex grammars lead to many classes and hard-to-maintain code.
- Performance is poor for frequent or complex interpretation.

#### Common Interview Q&A

**Q: Where is Interpreter used in .NET?**
> LINQ expression trees (`Expression<T>`) are the most prominent use of the Interpreter pattern in .NET. They represent code as data that can be analyzed and translated (e.g., to SQL by Entity Framework). Regex engines are also interpreters.

#### Common Mistakes
- Using Interpreter for complex grammars — use ANTLR or Roslyn instead.
- Not caching interpreted results when the same context is evaluated repeatedly.

#### Related Patterns
- **Composite** — Interpreter's expression tree IS a Composite structure.
- **Visitor** — used to traverse and evaluate the expression tree without modifying expression classes.

#### Key Takeaways
- Interpreter = represent grammar rules as objects and evaluate them recursively.
- Best for simple, stable grammars. Use dedicated parsers for complex languages.
- LINQ expression trees are the canonical .NET example.

---

## 16. Iterator

#### Intent
Provide a way to **sequentially access elements of a collection** without exposing its underlying representation.

#### Problem It Solves
- Client code uses different traversal logic for different collections (array, linked list, tree).
- The collection's internal structure should be hidden.
- Multiple traversal strategies should be possible (forward, reverse, filtered).

#### When to Use
- Traversing any collection without exposing internals.
- Providing multiple traversal strategies over the same collection.
- Iterating over a complex data structure (tree, graph).

#### When NOT to Use
- Simple arrays or lists where built-in `foreach` already works.
- When the traversal logic is trivial — adds unnecessary abstraction.

#### Real-World Analogy
A TV remote's channel-up button. You press it to move to the next channel. You do not know how channels are stored internally. The remote provides a uniform "next" interface regardless of the channel list format.

#### Enterprise Use Case
A paginated API result iterator that fetches pages lazily from an external API — the caller uses a simple `foreach` without knowing about pagination, HTTP calls, or page sizes.

#### C# Code Example

```csharp
// C# already provides IEnumerable<T> and IEnumerator<T> — this IS the Iterator pattern

// Custom iterator for paged data (real-world scenario)
public class PagedDataIterator : IEnumerable<string>
{
    private readonly List<List<string>> _pages;

    public PagedDataIterator(List<List<string>> pages) => _pages = pages;

    public IEnumerator<string> GetEnumerator()
    {
        foreach (var page in _pages)
            foreach (var item in page)
                yield return item; // lazy — fetches one item at a time
    }

    System.Collections.IEnumerator System.Collections.IEnumerable.GetEnumerator()
        => GetEnumerator();
}

// Usage — caller uses foreach; knows nothing about pages
var pages = new List<List<string>>
{
    new() { "Alice", "Bob" },
    new() { "Carol", "Dave" },
    new() { "Eve" }
};

var allUsers = new PagedDataIterator(pages);
foreach (var user in allUsers)
    Console.WriteLine(user); // Alice, Bob, Carol, Dave, Eve — seamlessly
```

#### Step-by-Step Explanation
1. `IEnumerable<T>` and `IEnumerator<T>` are .NET's built-in Iterator pattern interfaces.
2. `PagedDataIterator` wraps multi-page data and exposes it as a flat sequence.
3. `yield return` creates a state machine that pauses and resumes — lazy iteration.
4. Caller uses `foreach` — does not know about pages, indices, or the underlying structure.
5. Adding a new collection type (tree, linked list) just requires a new `IEnumerable<T>` implementation.

#### Advantages
- Uniform interface for traversing any collection.
- Supports multiple concurrent iterators (each has its own state).
- Lazy iteration with `yield return` is memory-efficient.

#### Disadvantages
- Adding the iterator can be overkill for simple collections.
- Does not support efficient random access.

#### Common Interview Q&A

**Q: How does C# implement the Iterator pattern natively?**
> `IEnumerable<T>` and `IEnumerator<T>` are the Iterator pattern. The `foreach` keyword calls `GetEnumerator()` internally. `yield return` creates a compiler-generated state machine that implements `IEnumerator<T>`.

**Q: What is the difference between Iterator and Enumerable?**
> `IEnumerable<T>` is the collection (can produce an iterator). `IEnumerator<T>` is the iterator (holds traversal state). A single collection can produce multiple independent iterators.

#### Common Mistakes
- Modifying the collection while iterating — causes `InvalidOperationException`.
- Not disposing `IEnumerator<T>` — use `foreach` or `using` to ensure disposal.

#### Related Patterns
- **Composite** — Iterator is often used to traverse Composite trees.
- **Factory Method** — `GetEnumerator()` is a factory method that produces an iterator.

#### Key Takeaways
- Iterator = uniform traversal interface without exposing collection internals.
- C#'s `IEnumerable<T>`, `IEnumerator<T>`, and `yield return` are native Iterator pattern.
- `foreach` is syntactic sugar over the Iterator pattern.

---

## 17. Mediator

#### Intent
Define an object (mediator) that **encapsulates how a set of objects interact**, promoting loose coupling by keeping objects from referring to each other explicitly.

#### Problem It Solves
- Many objects communicate directly with each other — a mess of cross-references (N×N coupling).
- Changing how one object communicates requires modifying many others.
- A complex communication network that is hard to understand and maintain.

#### When to Use
- Many objects communicate in complex ways.
- Reusing objects is difficult because they reference too many others.
- Chat rooms, air traffic control, event buses, CQRS with MediatR.

#### When NOT to Use
- Simple communication between two objects — direct reference is clearer.
- When the mediator itself becomes a God Object (knows too much).

#### Real-World Analogy
An air traffic control tower. Planes do not talk to each other directly. Each plane reports to the tower, and the tower coordinates all takeoffs and landings. Remove the tower, and planes must coordinate directly — chaos.

#### Enterprise Use Case
MediatR in an ASP.NET Core CQRS application. API controllers send commands/queries to MediatR. MediatR routes them to the correct handler. The controller does not know the handler; the handler does not know the controller.

#### C# Code Example

```csharp
// Mediator interface
public interface IChatMediator
{
    void SendMessage(string message, ChatUser sender);
    void Register(ChatUser user);
}

// Concrete mediator
public class ChatRoom : IChatMediator
{
    private List<ChatUser> _users = new();

    public void Register(ChatUser user) => _users.Add(user);

    public void SendMessage(string message, ChatUser sender)
    {
        foreach (var user in _users)
            if (user != sender) // don't echo back to sender
                user.Receive(message, sender.Name);
    }
}

// Colleague — knows only the mediator
public class ChatUser
{
    private readonly IChatMediator _mediator;
    public string Name { get; }

    public ChatUser(string name, IChatMediator mediator)
    {
        Name = name; _mediator = mediator;
        mediator.Register(this);
    }

    public void Send(string message) => _mediator.SendMessage(message, this);

    public void Receive(string message, string from)
        => Console.WriteLine($"{Name} received from {from}: {message}");
}

// Usage
var chatRoom = new ChatRoom();
var alice = new ChatUser("Alice", chatRoom);
var bob   = new ChatUser("Bob",   chatRoom);
var carol = new ChatUser("Carol", chatRoom);

alice.Send("Hello everyone!");
// Bob received from Alice: Hello everyone!
// Carol received from Alice: Hello everyone!
```

#### Step-by-Step Explanation
1. `IChatMediator` defines the mediation contract.
2. `ChatRoom` knows all users and routes messages — it is the hub.
3. `ChatUser` only knows the mediator — it sends via `_mediator.SendMessage()`.
4. No user has a reference to any other user — N×N coupling reduced to N×1.
5. Adding a new user just registers with the mediator — no other changes needed.

#### Advantages
- Reduces chaotic direct couplings to a single, manageable hub.
- Easy to change communication logic — only modify the mediator.
- Colleagues are easier to reuse — they have no direct dependencies on each other.

#### Disadvantages
- The mediator itself can become a God Object.
- Can obscure the flow of communication — hard to see who communicates with whom.

#### Common Interview Q&A

**Q: How does MediatR implement the Mediator pattern in ASP.NET Core?**
> Commands and queries are sent to `IMediator.Send()`. MediatR finds the registered handler (`IRequestHandler<TRequest, TResponse>`) and invokes it. The sender (controller) and the handler know nothing about each other — MediatR is the mediator.

**Q: What is the difference between Mediator and Observer?**
> In Observer, the subject broadcasts to all subscribers automatically. In Mediator, objects explicitly send messages through the mediator, which decides routing. Mediator is more controlled; Observer is more loosely coupled.

#### Common Mistakes
- Putting too much logic in the mediator — it becomes a God Object.
- Using Mediator when only two objects communicate — direct reference is simpler.

#### Related Patterns
- **Observer** — similar goal (loose coupling), different mechanism.
- **Facade** — also centralizes communication but to simplify, not to mediate peer-to-peer.
- **Command** — MediatR combines Command + Mediator.

#### Key Takeaways
- Mediator = centralized hub that eliminates direct N×N couplings between objects.
- MediatR library is the standard Mediator implementation for ASP.NET Core CQRS.
- Keep the mediator focused — avoid letting it become a God Object.

> **Summary — Behavioral Patterns (first half):** Chain of Responsibility (pipeline of handlers), Command (request as object with undo), Interpreter (evaluate grammar rules), Iterator (traverse collections uniformly), Mediator (central hub reduces N×N coupling). All five reduce coupling and define clear communication protocols.

---

## 18. Memento

#### Intent
**Capture and externalize an object's internal state** so it can be restored later, without violating encapsulation.

#### Problem It Solves
- You need to implement undo/redo, checkpoints, or snapshots.
- The object's internal state must be saved but its encapsulation must not be broken.
- You cannot make internal fields public just for the sake of snapshotting.

#### When to Use
- Undo/redo operations in editors (text, graphics, code).
- Game save states and checkpoints.
- Transaction rollback where object state must be restored on failure.

#### When NOT to Use
- When the state is very large — storing many snapshots is expensive.
- When the object frequently changes — memory consumption grows quickly.

#### Real-World Analogy
A video game save point. Before a difficult boss fight, the game saves your current state (health, weapons, position). If you die, you reload the save — your state is restored exactly as it was.

#### Enterprise Use Case
A text editor that supports Ctrl+Z undo. Each keystroke saves a `TextMemento`. Undo pops the last memento and restores the editor state.

#### C# Code Example

```csharp
// Memento — stores the state; only Originator can access internals
public class TextMemento
{
    public string Content { get; }
    public int    CursorPosition { get; }
    public TextMemento(string content, int cursor) { Content = content; CursorPosition = cursor; }
}

// Originator — the object whose state is saved/restored
public class TextEditor
{
    public string Content        { get; private set; } = "";
    public int    CursorPosition { get; private set; } = 0;

    public void Type(string text)
    {
        Content += text;
        CursorPosition = Content.Length;
        Console.WriteLine($"Content: '{Content}'");
    }

    public TextMemento Save() => new TextMemento(Content, CursorPosition);

    public void Restore(TextMemento memento)
    {
        Content        = memento.Content;
        CursorPosition = memento.CursorPosition;
        Console.WriteLine($"Restored to: '{Content}'");
    }
}

// Caretaker — manages the history; does not inspect memento contents
public class UndoManager
{
    private Stack<TextMemento> _history = new();

    public void Save(TextEditor editor) => _history.Push(editor.Save());

    public void Undo(TextEditor editor)
    {
        if (_history.TryPop(out var memento))
            editor.Restore(memento);
        else
            Console.WriteLine("Nothing to undo.");
    }
}

// Usage
var editor  = new TextEditor();
var undoMgr = new UndoManager();

undoMgr.Save(editor);
editor.Type("Hello");
undoMgr.Save(editor);
editor.Type(", World");
undoMgr.Save(editor);
editor.Type("!!!");

undoMgr.Undo(editor); // Restored to: 'Hello, World'
undoMgr.Undo(editor); // Restored to: 'Hello'
undoMgr.Undo(editor); // Restored to: ''
```

#### Step-by-Step Explanation
1. `TextMemento` captures the editor's state — its fields match the originator's internal state.
2. `TextEditor` (originator) creates and restores mementos — it controls its own state.
3. `UndoManager` (caretaker) holds the history stack without knowing what is inside each memento.
4. `Save()` is called before each change — creates a snapshot.
5. `Undo()` pops the last snapshot and restores the editor — encapsulation intact.

#### Advantages
- Undo/redo without violating encapsulation.
- Clean separation: originator manages state; caretaker manages history.

#### Disadvantages
- Memory cost — each snapshot stores a full copy of the state.
- If state is large, limit the history size (e.g., keep last 50 states only).

#### Common Interview Q&A

**Q: What is the difference between Memento and Command for undo?**
> Command stores what action to reverse (the undo operation). Memento stores the full state before the action. Memento is simpler to implement but uses more memory. Command undo is more memory-efficient but each command must define its own reverse logic.

**Q: How do you limit memory usage with Memento?**
> Cap the history stack size — discard the oldest entry when the limit is exceeded. Use differential snapshots (only store what changed) for large objects.

#### Common Mistakes
- Storing references instead of copies — the memento will reflect current state, not past state.
- Not clearing the history after a non-undoable action (like "save to file").

#### Related Patterns
- **Command** — alternative approach to undo using reverse operations.
- **Prototype** — also creates copies of objects; Prototype is for creating new objects, Memento for restoring state.

#### Key Takeaways
- Memento = snapshot of object state, restorable without breaking encapsulation.
- Three roles: Originator (creates/restores), Memento (stores state), Caretaker (manages history).
- Classic use case: Ctrl+Z in text editors and game checkpoints.

---

## 19. Observer

#### Intent
Define a **one-to-many dependency** between objects so that when one object changes state, all its dependents are notified and updated automatically.

#### Problem It Solves
- One object's state change must trigger updates in multiple others.
- You do not want the subject to be tightly coupled to its observers.
- The set of dependent objects should be dynamic — add/remove at runtime.

#### When to Use
- Event-driven systems: UI updates, notifications, real-time data feeds.
- When changes in one object require updating unknown-number-of-others.
- Publish-subscribe systems, domain events, message buses.

#### When NOT to Use
- Synchronous critical paths — observers add latency.
- When the order of notification matters and must be guaranteed.
- Simple one-to-one relationships — direct method call is clearer.

#### Real-World Analogy
A newspaper subscription. The newspaper (subject) publishes new issues. All subscribers (observers) receive it automatically. Subscribers can join or cancel anytime. The newspaper does not know who its subscribers are individually.

#### Enterprise Use Case
Domain events in DDD. When an `Order` is placed, the `OrderPlacedEvent` is raised. `InventoryService`, `EmailService`, and `AnalyticsService` are all observers — they react independently without the `Order` knowing about any of them.

#### C# Code Example

```csharp
// Observer interface
public interface IOrderObserver
{
    void OnOrderPlaced(Order order);
}

// Subject
public class Order
{
    private List<IOrderObserver> _observers = new();
    public int    Id       { get; set; }
    public string Customer { get; set; } = "";

    public void Subscribe(IOrderObserver observer)   => _observers.Add(observer);
    public void Unsubscribe(IOrderObserver observer) => _observers.Remove(observer);

    public void Place()
    {
        Console.WriteLine($"Order #{Id} placed by {Customer}");
        NotifyObservers(); // broadcast to all
    }

    private void NotifyObservers()
    {
        foreach (var obs in _observers)
            obs.OnOrderPlaced(this);
    }
}

// Concrete observers
public class InventoryObserver : IOrderObserver
{
    public void OnOrderPlaced(Order order)
        => Console.WriteLine($"[Inventory] Reserving stock for Order #{order.Id}");
}

public class EmailObserver : IOrderObserver
{
    public void OnOrderPlaced(Order order)
        => Console.WriteLine($"[Email] Sending confirmation to {order.Customer}");
}

// Usage
var order = new Order { Id = 101, Customer = "Alice" };
order.Subscribe(new InventoryObserver());
order.Subscribe(new EmailObserver());
order.Place();
// [Inventory] Reserving stock for Order #101
// [Email] Sending confirmation to Alice

// C# also has built-in: events and delegates ARE the Observer pattern
```

#### Step-by-Step Explanation
1. `IOrderObserver` defines the observer contract.
2. `Order` (subject) maintains a list of observers and notifies them on state change.
3. `Subscribe()`/`Unsubscribe()` allow dynamic observer management.
4. `Place()` triggers `NotifyObservers()` — iterates the list and calls each observer.
5. Observers are independent — adding a new one requires no change to `Order`.

> **C# native Observer:** `event` keyword and `EventHandler<T>` delegates are the language-level Observer pattern. `IObservable<T>` / `IObserver<T>` are the Rx (Reactive Extensions) version.

#### Advantages
- Loose coupling — subject knows only the observer interface.
- Dynamic subscriptions — add/remove at runtime.
- Open/Closed — add new observer types without changing the subject.

#### Disadvantages
- Observers may be notified in unpredictable order.
- Memory leaks if observers are not properly unsubscribed.
- Cascading updates — one notification triggers another, creating chains that are hard to debug.

#### Common Interview Q&A

**Q: How does C# implement Observer natively?**
> With `event` and `delegate`. `event Action<Order> OnOrderPlaced` is a built-in observer list. Subscribers add themselves with `+=` and remove with `-=`. `EventHandler<TEventArgs>` is the standardized form.

**Q: What is the difference between Observer and Mediator?**
> Observer: subjects broadcast; observers self-subscribe. Mediator: colleagues send through a central hub that controls routing. Observer is more decentralized; Mediator is more controlled.

**Q: How do you prevent memory leaks with events in C#?**
> Always unsubscribe with `-=` when the observer is no longer needed, or use `WeakReference`-based event systems. Failing to unsubscribe keeps the observer alive in memory even after it should be garbage collected.

#### Common Mistakes
- Forgetting to unsubscribe — causes memory leaks.
- Modifying the observer list inside a notification loop — causes `InvalidOperationException`.
- Performing heavy work in observer callbacks synchronously — blocks the subject.

#### Related Patterns
- **Mediator** — centralized routing vs. Observer's broadcast.
- **Event Bus** / **MediatR notifications** — Observer at the application level.

#### Key Takeaways
- Observer = one subject, many observers; state change triggers automatic notification.
- C# `event`/`delegate` is the language-native Observer.
- Always unsubscribe observers to prevent memory leaks.

---

## 20. State

#### Intent
Allow an object to **alter its behavior when its internal state changes**. The object will appear to change its class.

#### Problem It Solves
- Giant `if/switch` statements based on state fields scattered throughout the class.
- Adding a new state requires modifying multiple methods.
- State-specific behavior mixed with the main class logic — violates SRP.

#### When to Use
- Objects with significant state-dependent behavior (order status, traffic light, vending machine).
- Complex state machines where adding states should not require modifying existing ones.
- When state transitions are explicit and the rules matter.

#### When NOT to Use
- Simple two-state objects — a boolean flag with one `if` is fine.
- When states rarely change and state-specific logic is minimal.

#### Real-World Analogy
A traffic light. It has three states: Red, Yellow, Green. In each state, it behaves differently (stop, prepare, go). It transitions: Green → Yellow → Red → Green. The light itself does not change — its state does.

#### Enterprise Use Case
An `Order` entity with states: `Pending`, `Processing`, `Shipped`, `Delivered`, `Cancelled`. Each state allows different actions. In `Shipped` state, you can track but not cancel. In `Pending` state, you can cancel but not track.

#### C# Code Example

```csharp
// State interface
public interface IOrderState
{
    void ProcessPayment(Order order);
    void Ship(Order order);
    void Cancel(Order order);
}

// Order — the context
public class Order
{
    public IOrderState State { get; set; }
    public Order() => State = new PendingState();

    public void ProcessPayment() => State.ProcessPayment(this);
    public void Ship()           => State.Ship(this);
    public void Cancel()         => State.Cancel(this);
}

// Concrete states
public class PendingState : IOrderState
{
    public void ProcessPayment(Order order)
    {
        Console.WriteLine("Payment processed. Moving to Processing.");
        order.State = new ProcessingState();
    }
    public void Ship(Order order)   => Console.WriteLine("Cannot ship — payment not received.");
    public void Cancel(Order order) { Console.WriteLine("Order cancelled."); order.State = new CancelledState(); }
}

public class ProcessingState : IOrderState
{
    public void ProcessPayment(Order order) => Console.WriteLine("Payment already done.");
    public void Ship(Order order)
    {
        Console.WriteLine("Order shipped.");
        order.State = new ShippedState();
    }
    public void Cancel(Order order) => Console.WriteLine("Cannot cancel — already processing.");
}

public class ShippedState : IOrderState
{
    public void ProcessPayment(Order order) => Console.WriteLine("Already paid.");
    public void Ship(Order order)   => Console.WriteLine("Already shipped.");
    public void Cancel(Order order) => Console.WriteLine("Cannot cancel — already shipped.");
}

public class CancelledState : IOrderState
{
    public void ProcessPayment(Order order) => Console.WriteLine("Order is cancelled.");
    public void Ship(Order order)   => Console.WriteLine("Order is cancelled.");
    public void Cancel(Order order) => Console.WriteLine("Already cancelled.");
}

// Usage
var order = new Order();
order.Ship();           // Cannot ship — payment not received.
order.ProcessPayment(); // Payment processed → Processing
order.Ship();           // Order shipped → Shipped
order.Cancel();         // Cannot cancel — already shipped.
```

#### Step-by-Step Explanation
1. `IOrderState` declares all behaviors that vary by state.
2. Each state class implements the full interface with state-appropriate responses.
3. `Order` (context) holds a `State` reference and delegates all calls to it.
4. State transitions happen inside state classes (`order.State = new ProcessingState()`).
5. Adding a new state = add a new class. No `switch` statement, no modification to `Order`.

#### Advantages
- Eliminates complex conditionals.
- Each state has its own class — SRP, OCP.
- State transitions are explicit and centralized.

#### Disadvantages
- Many small state classes.
- State transitions scattered across state classes can be hard to trace.

#### Common Interview Q&A

**Q: What is the difference between State and Strategy?**
> Both use polymorphism to change behavior. Strategy is chosen by the client and does not change itself (algorithm selection). State changes automatically as the object's internal state transitions — the state drives its own transition. See the comparison section for the full table.

**Q: Where is State used in .NET?**
> Task state machine (`Task.Status`), HTTP connection state, ASP.NET Core request lifecycle, and workflow engines (e.g., Elsa Workflows) all use State pattern or state machines.

#### Common Mistakes
- Keeping state transition logic in the context (Order) instead of in state classes.
- Sharing mutable state class instances across multiple context objects.

#### Related Patterns
- **Strategy** — similar structure; different semantics (chosen vs. self-transitioning).
- **Command** — can trigger state transitions.

#### Key Takeaways
- State = object behavior changes as internal state changes, eliminating conditionals.
- Each state is a class; transitions happen inside state methods.
- Use it to replace state-based `switch` statements in domain objects.

---

## 21. Strategy

#### Intent
Define a family of algorithms, encapsulate each one, and make them **interchangeable** at runtime.

#### Problem It Solves
- Multiple algorithms exist for the same task (sorting, compression, payment, pricing).
- Algorithms are selected at runtime based on context.
- Adding a new algorithm requires modifying a class full of conditionals.

#### When to Use
- Multiple variants of an algorithm or behavior.
- Runtime selection of algorithm (user choice, configuration, A/B test).
- Eliminating a switch/if-else chain that selects between algorithms.

#### When NOT to Use
- Only one algorithm exists or ever will — needless abstraction.
- When the algorithms are trivially different — just use a function/delegate.

#### Real-World Analogy
Navigation apps. You select a route strategy: Fastest, Shortest, or Avoid Highways. The destination is the same — only the algorithm to get there changes. You pick the strategy at runtime.

#### Enterprise Use Case
A discount engine in e-commerce. Different pricing strategies: `PercentageDiscount`, `FixedAmountDiscount`, `BuyOneGetOneDiscount`. Selected based on a promotion code or customer tier.

#### C# Code Example

```csharp
// Strategy interface
public interface IDiscountStrategy
{
    decimal ApplyDiscount(decimal originalPrice);
}

// Concrete strategies
public class PercentageDiscount : IDiscountStrategy
{
    private readonly decimal _percent;
    public PercentageDiscount(decimal percent) => _percent = percent;
    public decimal ApplyDiscount(decimal price) => price * (1 - _percent / 100);
}

public class FixedDiscount : IDiscountStrategy
{
    private readonly decimal _amount;
    public FixedDiscount(decimal amount) => _amount = amount;
    public decimal ApplyDiscount(decimal price) => Math.Max(0, price - _amount);
}

public class NoDiscount : IDiscountStrategy
{
    public decimal ApplyDiscount(decimal price) => price;
}

// Context — uses a strategy
public class ShoppingCart
{
    private IDiscountStrategy _strategy;

    public ShoppingCart(IDiscountStrategy strategy) => _strategy = strategy;

    public void SetStrategy(IDiscountStrategy strategy) => _strategy = strategy;

    public decimal Checkout(decimal subtotal)
    {
        var total = _strategy.ApplyDiscount(subtotal);
        Console.WriteLine($"Subtotal: ${subtotal} → Total after discount: ${total}");
        return total;
    }
}

// Usage
var cart = new ShoppingCart(new PercentageDiscount(20));
cart.Checkout(100m); // $80

cart.SetStrategy(new FixedDiscount(15));
cart.Checkout(100m); // $85

cart.SetStrategy(new NoDiscount());
cart.Checkout(100m); // $100
```

#### Step-by-Step Explanation
1. `IDiscountStrategy` defines the common interface for all algorithms.
2. Each concrete strategy encapsulates one discount algorithm.
3. `ShoppingCart` (context) holds a strategy reference and delegates to it.
4. `SetStrategy()` allows runtime strategy swapping.
5. Adding a new discount type = add a new class, no changes to `ShoppingCart`.

#### Advantages
- Eliminates conditional logic in the context.
- OCP — add new strategies without modifying the context.
- Strategies are independently testable.
- Runtime swappability.

#### Disadvantages
- Client must know which strategy to use.
- More classes than a simple `if/else` for very small cases.

#### Common Interview Q&A

**Q: How is Strategy different from simple function delegation in C#?**
> Strategy uses polymorphism — the strategy implements an interface. For simple cases, C# `Func<decimal, decimal>` achieves the same result without a full interface. Use Strategy when the algorithm has complex state or multiple methods; use `Func<>` for simple single-method variation.

**Q: Where is Strategy used in .NET?**
> `IComparer<T>` and `IEqualityComparer<T>` in LINQ sorting are Strategy pattern. `JsonSerializerOptions` strategies, `HttpMessageHandler` chains, and ASP.NET Core authentication schemes are all strategy-based.

**Q: Explain the difference between Strategy and State.**
> Strategy is chosen by the client and typically stays fixed for the context's lifetime. State transitions automatically based on the object's condition — the state drives its own change. See the comparison section.

#### Common Mistakes
- Making strategies aware of each other — they should be fully independent.
- Choosing Strategy when the behavior never changes — needless interface.
- Injecting strategies that hold shared mutable state — strategies should be stateless or hold only config.

#### Related Patterns
- **State** — similar structure; strategies are external choices, states are internal transitions.
- **Template Method** — defines the skeleton in a base class; Strategy defines the whole algorithm in a separate class.
- **Factory Method** — often used to create the right strategy at runtime.

#### Key Takeaways
- Strategy = family of interchangeable algorithms behind a common interface.
- Eliminates type-checking conditionals and supports runtime algorithm swapping.
- `IComparer<T>`, `IEqualityComparer<T>` in .NET BCL are canonical Strategy examples.

---

## 22. Template Method

#### Intent
Define the **skeleton of an algorithm** in a base class, deferring some steps to subclasses. Subclasses can override specific steps without changing the algorithm's overall structure.

#### Problem It Solves
- Multiple classes share the same algorithm structure but differ in specific steps.
- Code duplication across subclasses for the invariant parts of an algorithm.
- You want to enforce an algorithm's sequence while allowing customization of individual steps.

#### When to Use
- Multiple classes do the same thing with minor step variations (report generation, data import pipelines).
- You want to enforce a fixed sequence of steps.
- Framework hooks where users override specific steps (e.g., ASP.NET Controller lifecycle).

#### When NOT to Use
- When subclasses need to change the overall algorithm structure — use Strategy instead.
- Deep inheritance chains — Template Method relies on inheritance, which can become brittle.

#### Real-World Analogy
An assembly line in a factory. The sequence is fixed: stamp → paint → inspect → package. The specific type of stamping or painting varies per product, but the sequence never changes. The line is the template; each station's implementation varies.

#### Enterprise Use Case
A data export pipeline. The skeleton: open connection → read data → transform → write output → close connection. The transformation step varies: CSV export transforms differently from XML export.

#### C# Code Example

```csharp
// Abstract class defines the template method (the skeleton)
public abstract class DataExporter
{
    // Template method — the fixed sequence
    public void Export(string destination)
    {
        Connect();
        var data = ReadData();
        var transformed = Transform(data);
        WriteOutput(transformed, destination);
        Disconnect();
    }

    protected abstract void Connect();
    protected abstract string ReadData();
    protected abstract string Transform(string data); // varies per subclass
    protected abstract void WriteOutput(string data, string destination);

    protected virtual void Disconnect() // optional hook — default implementation
        => Console.WriteLine("Connection closed.");
}

// Concrete implementation — CSV
public class CsvExporter : DataExporter
{
    protected override void Connect()    => Console.WriteLine("Connecting to SQL Server...");
    protected override string ReadData() { Console.WriteLine("Reading rows..."); return "id,name\n1,Alice"; }
    protected override string Transform(string data) => data; // CSV needs no transformation
    protected override void WriteOutput(string data, string destination)
        => Console.WriteLine($"Writing CSV to {destination}:\n{data}");
}

// Concrete implementation — XML
public class XmlExporter : DataExporter
{
    protected override void Connect()    => Console.WriteLine("Connecting to Oracle DB...");
    protected override string ReadData() { Console.WriteLine("Reading rows..."); return "1|Alice"; }
    protected override string Transform(string data)
    {
        var parts = data.Split('|');
        return $"<record><id>{parts[0]}</id><name>{parts[1]}</name></record>";
    }
    protected override void WriteOutput(string data, string destination)
        => Console.WriteLine($"Writing XML to {destination}:\n{data}");
}

// Usage
DataExporter exporter = new CsvExporter();
exporter.Export("output.csv");

Console.WriteLine("---");

exporter = new XmlExporter();
exporter.Export("output.xml");
```

#### Step-by-Step Explanation
1. `Export()` is the template method — it defines the fixed sequence of steps.
2. Abstract steps (`Connect`, `ReadData`, `Transform`, `WriteOutput`) are mandatory overrides.
3. `Disconnect()` is a virtual hook — has a default implementation; subclasses can override it.
4. `CsvExporter` and `XmlExporter` only override the steps that differ.
5. The sequence (connect → read → transform → write → disconnect) is enforced in the base class.

#### Advantages
- Eliminates code duplication for the common algorithm skeleton.
- Subclasses only implement what is different.
- Easy to add a new implementation — just add a new subclass.

#### Disadvantages
- Relies on inheritance — tight coupling to base class.
- Violates Liskov if a subclass override changes behavior unexpectedly.
- Difficult to test the base class in isolation.

#### Common Interview Q&A

**Q: What is the difference between Template Method and Strategy?**
> Template Method uses inheritance — the algorithm lives in a base class; subclasses fill in the steps. Strategy uses composition — the whole algorithm is in a separate object injected into the context. Template Method is compile-time; Strategy is runtime-swappable.

**Q: What are hooks in Template Method?**
> Hooks are virtual methods with empty or default implementations in the base class. Subclasses can override them optionally — they are extension points. `Disconnect()` in the example above is a hook. In ASP.NET Core, Controller lifecycle methods (`OnActionExecuting`, `OnActionExecuted`) are hooks.

**Q: Where is Template Method used in .NET?**
> `Stream.Read()` is a template method — subclasses implement the specifics. ASP.NET Core's `Controller.OnActionExecuting()`, Entity Framework's `DbContext.SaveChanges()`, and `BackgroundService.ExecuteAsync()` all use template method hooks.

#### Common Mistakes
- Allowing subclasses to override the template method itself — use `sealed` on the template method to prevent this.
- Having too many abstract steps — becomes hard to subclass correctly.
- Choosing Template Method when Strategy would give more flexibility (can swap at runtime).

#### Related Patterns
- **Strategy** — uses composition instead of inheritance; runtime-swappable.
- **Factory Method** — Template Method applied specifically to object creation.

#### Key Takeaways
- Template Method = fixed algorithm skeleton in base class; variable steps in subclasses.
- Seal the template method to prevent subclasses from breaking the sequence.
- Use Strategy instead if you need runtime flexibility (no inheritance required).

---

## 23. Visitor

#### Intent
Define a new operation to be performed on elements of an object structure **without changing the classes** of those elements.

#### Problem It Solves
- You have a stable set of classes (an object structure) but need to add new operations to them frequently.
- Adding the operation directly to every class violates OCP and causes class pollution.
- You need to perform different, unrelated operations on a composite structure without cluttering the element classes.

#### When to Use
- You have a stable object hierarchy but frequently add new operations.
- Operations span many different classes (report generation, serialization, type checking across an AST).
- You need double dispatch — behavior depends on both the visitor type and the element type.

#### When NOT to Use
- When the object hierarchy changes frequently — adding a new element requires updating every visitor.
- When the operations are few and simple — direct methods on the classes are cleaner.

#### Real-World Analogy
A tax auditor visiting different types of businesses (restaurant, shop, factory). The auditor (visitor) applies different tax calculations to each type of business. The businesses (elements) do not change — only the auditor's visit logic varies per business type.

#### Enterprise Use Case
An AST (Abstract Syntax Tree) in a compiler or expression evaluator. Different visitors perform different operations: `PrintVisitor` prints the tree, `EvaluateVisitor` computes the value, `OptimizeVisitor` simplifies expressions — without touching the node classes.

#### C# Code Example

```csharp
// Element interface — must accept any visitor
public interface IDocumentElement
{
    void Accept(IDocumentVisitor visitor);
}

// Concrete elements — the stable hierarchy
public class TextElement : IDocumentElement
{
    public string Content { get; }
    public TextElement(string content) => Content = content;
    public void Accept(IDocumentVisitor visitor) => visitor.Visit(this);
}

public class ImageElement : IDocumentElement
{
    public string FileName { get; }
    public int Width { get; }
    public ImageElement(string file, int width) { FileName = file; Width = width; }
    public void Accept(IDocumentVisitor visitor) => visitor.Visit(this);
}

// Visitor interface — one Visit() per element type
public interface IDocumentVisitor
{
    void Visit(TextElement text);
    void Visit(ImageElement image);
}

// Concrete visitor — HTML export
public class HtmlExportVisitor : IDocumentVisitor
{
    public void Visit(TextElement text)   => Console.WriteLine($"<p>{text.Content}</p>");
    public void Visit(ImageElement image) => Console.WriteLine($"<img src='{image.FileName}' width='{image.Width}'/>");
}

// Concrete visitor — Word count
public class WordCountVisitor : IDocumentVisitor
{
    public int Count { get; private set; }
    public void Visit(TextElement text)   => Count += text.Content.Split(' ').Length;
    public void Visit(ImageElement image) { /* images have no words */ }
}

// Usage
var document = new List<IDocumentElement>
{
    new TextElement("Hello World"),
    new ImageElement("logo.png", 200),
    new TextElement("Design Patterns are great")
};

var htmlVisitor = new HtmlExportVisitor();
foreach (var el in document) el.Accept(htmlVisitor);

var wordCounter = new WordCountVisitor();
foreach (var el in document) el.Accept(wordCounter);
Console.WriteLine($"Word count: {wordCounter.Count}"); // 7
```

#### Step-by-Step Explanation
1. `IDocumentElement` requires `Accept(IDocumentVisitor)` — the element accepts any visitor.
2. `TextElement` and `ImageElement` call `visitor.Visit(this)` — passing themselves to the visitor.
3. `IDocumentVisitor` has one `Visit()` overload per element type.
4. `HtmlExportVisitor` renders each element as HTML. `WordCountVisitor` counts words.
5. Adding a new operation = add a new visitor. No change to element classes.

#### Advantages
- Add new operations without modifying element classes (OCP).
- Keeps related operations together in one visitor class (SRP).
- Visitor accumulates state across multiple elements easily (e.g., `WordCountVisitor.Count`).

#### Disadvantages
- Adding a new element type requires updating every visitor — inverse of OCP for elements.
- Breaks encapsulation — visitors need access to element internals.
- Complex to understand if the hierarchy is large.

#### Common Interview Q&A

**Q: What is double dispatch and why does Visitor use it?**
> Standard method calls in C# dispatch based on one type (the runtime type of the object). Visitor achieves double dispatch — behavior depends on both the visitor type AND the element type. `element.Accept(visitor)` → `visitor.Visit(this)` — two runtime lookups, not one.

**Q: When would you choose Visitor over just adding a method to each element class?**
> When operations are many and the hierarchy is stable. Adding 10 operations as methods would clutter every element class. One visitor class per operation keeps elements clean and operations grouped.

**Q: Where is Visitor used in .NET?**
> Roslyn (the .NET compiler) uses the Visitor pattern extensively to traverse and analyze C# syntax trees. `CSharpSyntaxVisitor` is the visitor base class. Expression tree visitors in EF Core translate LINQ to SQL using visitors.

#### Common Mistakes
- Using Visitor when the hierarchy changes often — every new element breaks all visitors.
- Forgetting to call `Accept()` on all elements in the structure.
- Putting business logic in the element classes instead of in visitors.

#### Related Patterns
- **Composite** — Visitor is often applied to Composite structures.
- **Interpreter** — Visitor is used to add operations to the expression tree (Interpreter's structure).
- **Iterator** — used to traverse the structure before calling `Accept()`.

#### Key Takeaways
- Visitor = new operations on a stable class hierarchy without modifying element classes.
- Uses double dispatch: `Accept(visitor)` → `visitor.Visit(this)`.
- Roslyn (the C# compiler API) is the most prominent .NET implementation.

> **Summary — Behavioral Patterns (second half):** Memento (snapshot for undo), Observer (one-to-many notifications), State (behavior changes with internal state), Strategy (interchangeable algorithms), Template Method (fixed skeleton, variable steps), Visitor (add operations to stable hierarchy). All six manage how objects communicate change and responsibility.

---

# Part 5 — Essential Interview Comparisons

---

## Factory Method vs Abstract Factory

| Aspect | Factory Method | Abstract Factory |
|---|---|---|
| **Mechanism** | Inheritance — subclasses override a factory method | Composition — a factory object creates products |
| **Products created** | One product type | A family of related product types |
| **How to extend** | Add a new subclass | Add a new concrete factory |
| **Coupling** | Creator and product in same hierarchy | Factory and products are separate |
| **Complexity** | Simpler | More complex — more classes |
| **Use when** | "Which one type to create?" | "Which family to use consistently?" |

**Rule of thumb:**
- One product type → Factory Method.
- Multiple related product types that must be consistent → Abstract Factory.

```csharp
// Factory Method — one product
public abstract class NotifierFactory
{
    public abstract INotifier Create(); // one product
}

// Abstract Factory — a family of products
public interface IInfrastructureFactory
{
    ILogger CreateLogger();       // product 1
    ICache  CreateCache();        // product 2
    IQueue  CreateMessageQueue(); // product 3
}
```

---

## Adapter vs Facade

| Aspect | Adapter | Facade |
|---|---|---|
| **Purpose** | Make incompatible interfaces compatible | Simplify a complex subsystem |
| **Existing interfaces** | Works with two existing interfaces | Creates a NEW simplified interface |
| **Number of classes** | Usually wraps one class | Wraps multiple classes |
| **Interface change** | Translates/converts the interface | Hides the interface behind simplicity |
| **Client knowledge** | Client knows the target interface | Client uses only the facade |
| **Design time** | Reactive — fixes incompatibility | Proactive — designed upfront |

```csharp
// Adapter — translates ILegacyLogger to ILogger
public class LoggerAdapter : ILogger
{
    private readonly LegacyLogger _legacy;
    public void Log(string msg) => _legacy.WriteLog(DateTime.Now, msg); // translation
}

// Facade — hides InventoryService, PaymentService, ShippingService
public class OrderFacade
{
    public bool PlaceOrder(...) { /* orchestrates 4 services */ }
}
```

---

## Strategy vs State

| Aspect | Strategy | State |
|---|---|---|
| **Intent** | Choose an algorithm externally | Change behavior based on internal state |
| **Who changes it** | Client selects the strategy | State transitions itself |
| **Awareness** | Strategies are unaware of each other | States know about each other (for transitions) |
| **Lifecycle** | Typically fixed for the context's use | Transitions automatically during use |
| **Number of implementations** | Multiple interchangeable algorithms | Multiple state-specific behaviors |
| **Use when** | "Pick algorithm A, B, or C" | "Behavior changes as object evolves" |

```csharp
// Strategy — client picks; stays fixed
cart.SetStrategy(new PercentageDiscount(10)); // external choice

// State — object drives its own transition
order.ProcessPayment(); // internally: State = new ProcessingState()
```

**Simple memory trick:** Strategy = "You choose." State = "I decide when to change."

---

## Decorator vs Proxy

| Aspect | Decorator | Proxy |
|---|---|---|
| **Intent** | Add behavior to an object | Control access to an object |
| **Interface** | Same as the component | Same as the subject |
| **Knowledge of real object** | Creates real object externally | Often creates/controls the real object |
| **Nesting** | Can be stacked multiple layers | Usually one proxy per subject |
| **Use when** | "I want to add logging/caching/retry" | "I want lazy load/auth/remote access" |
| **Common C# examples** | Middleware, Stream decorators | EF Core lazy loading, DynamicProxy |

```csharp
// Decorator — wraps and adds behavior (logging)
public class LoggingRepo : IRepository
{
    private readonly IRepository _inner;
    public User Get(int id) { Log("Getting..."); return _inner.Get(id); }
}

// Proxy — controls access (lazy loading)
public class LazyRepo : IRepository
{
    private SqlRepository? _real; // created only on first access
    public User Get(int id) => (_real ??= new SqlRepository()).Get(id);
}
```

---

## Composition vs Inheritance in Design Patterns

| Aspect | Inheritance-based patterns | Composition-based patterns |
|---|---|---|
| **Patterns** | Template Method, Factory Method | Strategy, Decorator, Composite, Bridge |
| **Coupling** | Tight — base changes affect all subclasses | Loose — components are swappable |
| **Runtime flexibility** | Fixed at compile time | Swappable at runtime |
| **Testing** | Harder — must test via subclass | Easier — inject mocks |
| **GoF guidance** | "Favor composition over inheritance" | Preferred for most patterns |

**Key insight:** The GoF book itself says "Favor composition over inheritance." Most patterns in the catalog achieve flexibility through composition, not inheritance. Inheritance-based patterns (Template Method) are the minority.

---

# Part 6 — Advanced Interview Topics

---

## Dependency Injection and Design Patterns

DI is not a design pattern itself — it is a technique that enables and combines several patterns:

| DI enables | Pattern |
|---|---|
| Inject `IPaymentProcessor` | Strategy — swap implementations at runtime |
| Inject `ILogger` | Decorator — wrap with logging, metrics |
| Register `IOrderRepository` | Factory / Abstract Factory — container creates instances |
| `AddSingleton<IConfig>` | Singleton — one instance per app lifetime |
| `AddScoped<IUnitOfWork>` | Unit of Work — scoped to one HTTP request |

**In ASP.NET Core:** The DI container (`IServiceCollection`) IS an Abstract Factory. It creates families of services (scoped, singleton, transient) and wires them together based on registrations.

```csharp
// Registering a Strategy via DI
builder.Services.AddScoped<IDiscountStrategy, PercentageDiscount>();

// Registering a Decorator via DI (manually)
builder.Services.AddScoped<SqlOrderRepository>();
builder.Services.AddScoped<IOrderRepository>(sp =>
    new LoggingRepository(
        new CachingRepository(sp.GetRequiredService<SqlOrderRepository>())));
```

---

## How Modern Frameworks Already Use Design Patterns

| Framework feature | Pattern used |
|---|---|
| ASP.NET Core Middleware | Chain of Responsibility |
| `ILogger<T>` | Abstract Factory + Adapter |
| `HttpClient` with `HttpMessageHandler` | Decorator + Chain of Responsibility |
| Entity Framework lazy loading | Proxy (Virtual) |
| MediatR | Mediator + Command |
| ASP.NET Core routing | Chain of Responsibility |
| `IEnumerable<T>` / `yield return` | Iterator |
| `event` / `EventHandler<T>` | Observer |
| `BackgroundService.ExecuteAsync()` | Template Method |
| `IOptions<T>` configuration | Facade + Singleton |
| `Func<T>`, `Action<T>` delegates | Strategy (lightweight) |
| Roslyn `CSharpSyntaxVisitor` | Visitor |

---

## Most Commonly Used Patterns in ASP.NET Core

In order of frequency in real enterprise codebases:

1. **Repository** — data access abstraction (not GoF, but ubiquitous)
2. **Strategy** — payment, discount, export, auth schemes
3. **Decorator** — logging, caching, retry wrappers around services
4. **Factory Method** — service/client creation via `IHttpClientFactory`, `ILoggerFactory`
5. **Observer** — domain events, SignalR notifications, `INotifyPropertyChanged`
6. **Mediator** — MediatR for CQRS commands and queries
7. **Chain of Responsibility** — middleware pipeline (authentication, CORS, routing)
8. **Proxy** — EF Core lazy loading, Castle Windsor dynamic proxies
9. **Builder** — `WebApplicationBuilder`, `IHostBuilder`, `HttpRequestMessage`
10. **Facade** — service classes wrapping multiple repositories/APIs

---

## Patterns Overused by Junior Developers

| Pattern | Why juniors overuse it | When it is actually needed |
|---|---|---|
| **Singleton** | "Global access is convenient" | Only when exactly one instance is semantically required |
| **Factory** | "Always abstract object creation" | Only when creation type varies or is complex |
| **Abstract Factory** | "We might support multiple UIs" | Only when you have two+ concrete families now |
| **Strategy** | "Every `if` should be a strategy" | Only when the algorithm varies meaningfully |
| **Observer** | "Events everywhere" | Only when true many-to-many notification is needed |
| **Facade** | "Every feature needs a facade" | Only when the subsystem is genuinely complex |

**Senior developer rule:** "Patterns are solutions to problems, not features to add to every class." Apply YAGNI — you ain't gonna need it until you actually need it.

---

## How to Identify Patterns in Existing Codebases

Use these signals to recognize patterns when reading unfamiliar code:

| Signal | Likely pattern |
|---|---|
| Class with private constructor + static `Instance` property | Singleton |
| Abstract class with `Create()` method returning an interface | Factory Method |
| Interface with multiple `Create*()` methods for related types | Abstract Factory |
| Fluent builder with `Build()` at the end | Builder |
| Class with `Clone()` returning its own type | Prototype |
| Class implementing another's interface via wrapping | Adapter |
| Abstract class with `_impl` reference and both hierarchies vary | Bridge |
| Recursive structure where leaves and branches share an interface | Composite |
| Class wrapping same interface, adding behavior before/after delegate | Decorator |
| Single entry-point class that coordinates many subsystem calls | Facade |
| Factory that returns a cached, shared instance | Flyweight |
| Class implementing same interface as the wrapped object, with access checks | Proxy |
| `_next?.Handle(request)` inside a handler | Chain of Responsibility |
| Class encapsulating an action with `Execute()` and `Undo()` | Command |
| Objects interpreting a rule with `Interpret(context)` | Interpreter |
| `IEnumerator<T>` with `MoveNext()` and `Current` | Iterator |
| Central coordinator with `Send(message, sender)` | Mediator |
| `Save()` returning a snapshot and `Restore(snapshot)` | Memento |
| Subscribe/Unsubscribe + notify loop | Observer |
| Object holding a state object that changes itself | State |
| Interface + multiple implementations + context that delegates | Strategy |
| Base class with `sealed` orchestrating method calling abstract steps | Template Method |
| `Accept(visitor)` → `visitor.Visit(this)` | Visitor |

---

# Final Cheat Sheet

> Use this for a 10-minute revision before any interview.

---

## All 23 Patterns — Quick Reference

| # | Pattern | Category | One-line intent | Key C# example |
|---|---|---|---|---|
| 1 | **Singleton** | Creational | One instance, global access | `ConfigurationManager.Instance` |
| 2 | **Factory Method** | Creational | Subclass decides what to create | `ILoggerFactory.CreateLogger<T>()` |
| 3 | **Abstract Factory** | Creational | Create a consistent family of objects | `DbProviderFactory` |
| 4 | **Builder** | Creational | Step-by-step complex object construction | `WebApplication.CreateBuilder()` |
| 5 | **Prototype** | Creational | Clone instead of construct | `record with { }` expression |
| 6 | **Adapter** | Structural | Translate one interface to another | Legacy logger wrapper |
| 7 | **Bridge** | Structural | Separate abstraction from implementation | Notification type + channel |
| 8 | **Composite** | Structural | Tree of leaf/branch nodes sharing one interface | File system hierarchy |
| 9 | **Decorator** | Structural | Add behavior by wrapping | Caching + Logging repository |
| 10 | **Facade** | Structural | Simple interface to complex subsystem | `OrderFacade.PlaceOrder()` |
| 11 | **Flyweight** | Structural | Share intrinsic state across many objects | String interning, tree types |
| 12 | **Proxy** | Structural | Control access to an object | EF Core lazy loading |
| 13 | **Chain of Responsibility** | Behavioral | Pipeline of handlers | ASP.NET Core middleware |
| 14 | **Command** | Behavioral | Encapsulate request as object with undo | MediatR commands |
| 15 | **Interpreter** | Behavioral | Evaluate grammar rules | LINQ expression trees |
| 16 | **Iterator** | Behavioral | Traverse collection without exposing internals | `IEnumerable<T>`, `foreach` |
| 17 | **Mediator** | Behavioral | Central hub eliminates N×N coupling | MediatR, ChatRoom |
| 18 | **Memento** | Behavioral | Snapshot for undo without breaking encapsulation | Ctrl+Z, game save |
| 19 | **Observer** | Behavioral | One subject, many auto-notified observers | `event`, domain events |
| 20 | **State** | Behavioral | Behavior changes with internal state | Order status machine |
| 21 | **Strategy** | Behavioral | Interchangeable algorithms at runtime | `IDiscountStrategy`, `IComparer<T>` |
| 22 | **Template Method** | Behavioral | Fixed skeleton, variable steps in subclasses | Data export pipeline |
| 23 | **Visitor** | Behavioral | Add operations to stable hierarchy | Roslyn syntax visitors |

---

## Pattern Selection Decision Guide

```
Need to create objects?
├── One instance globally?                          → Singleton
├── Create type varies, one product?                → Factory Method
├── Create consistent family of related products?  → Abstract Factory
├── Many optional parameters / multi-step build?   → Builder
└── Copy expensive object?                          → Prototype

Need to structure objects?
├── Incompatible interfaces?                        → Adapter
├── Two varying dimensions (type × platform)?      → Bridge
├── Part-whole tree, uniform treatment?            → Composite
├── Add behavior dynamically (logging, cache)?     → Decorator
├── Simplify a complex subsystem?                  → Facade
├── Many similar objects, share state?             → Flyweight
└── Control object access?                         → Proxy

Need to coordinate behavior?
├── Pipeline, each step may stop chain?            → Chain of Responsibility
├── Undo/redo, queuing, scheduling?                → Command
├── Evaluate a grammar / rule language?            → Interpreter
├── Traverse a collection uniformly?               → Iterator
├── Reduce N×N coupling, central hub?              → Mediator
├── Snapshot for undo without breaking encaps.?   → Memento
├── Broadcast state change to many?                → Observer
├── Object behavior changes by internal state?     → State
├── Swap algorithm at runtime?                     → Strategy
├── Fixed algorithm, variable steps?               → Template Method
└── Add operations to stable hierarchy?            → Visitor
```

---

## Memory Mnemonics

**Creational — "SFABP" (Some Factories Are Building Prototypes)**
- **S**ingleton, **F**actory Method, **A**bstract Factory, **B**uilder, **P**rototype

**Structural — "ABCDFP" + Flyweight (ABC Decorates Facades, Proxies Fly)**
- **A**dapter, **B**ridge, **C**omposite, **D**ecorator, **F**acade, Fly**w**eight, **P**roxy

**Behavioral — "CoC-I-Im-M-MOST-V" (Chain Commands; Interpret, Iterate, Mediate; Memo, Observer, State, Strategy, Template, Visitor)**
- **C**hain of Responsibility, **C**ommand, **I**nterpreter, **I**terator, **M**ediator, **M**emento, **O**bserver, **S**tate, **S**trategy, **T**emplate Method, **V**isitor

---

## Key Interview Answers — Quick Reference

| Question | Strong answer |
|---|---|
| What are design patterns? | Proven, named solutions to recurring design problems — vocabulary, not code |
| Creational vs Structural vs Behavioral? | Create objects / Compose objects / Coordinate behavior |
| Factory Method vs Abstract Factory? | One product vs a family of products |
| Adapter vs Facade? | Translate interface vs simplify interface |
| Strategy vs State? | Client chooses vs object self-transitions |
| Decorator vs Proxy? | Add behavior vs control access |
| When NOT to use patterns? | When the problem they solve is absent — avoid premature abstraction (YAGNI) |
| Where is Observer in .NET? | `event` keyword, `INotifyPropertyChanged`, SignalR |
| Where is Chain of Responsibility? | ASP.NET Core middleware pipeline |
| Where is Strategy? | `IComparer<T>`, auth schemes, discount engines |
| Where is Builder? | `WebApplicationBuilder`, `StringBuilder`, HTTP request building |
| Where is Visitor in .NET? | Roslyn `CSharpSyntaxVisitor`, EF Core expression tree visitors |
| Most common pattern in enterprise .NET? | Repository, Strategy, Decorator, Mediator (MediatR) |

---

*End of guide. Good luck in your interviews.*
