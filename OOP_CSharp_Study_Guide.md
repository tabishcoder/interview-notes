# Object-Oriented Programming in C# — Complete Interview Study Guide

> **How to use this guide:**
> Read it top-to-bottom once for understanding. Then use the section summaries and final cheat sheet for quick revision before interviews. Every code example is minimal and focused — understand the concept, not just the syntax.

---

## Table of Contents

1. [Core OOP Pillars](#core-oop-pillars)
   - [Encapsulation](#1-encapsulation)
   - [Inheritance](#2-inheritance)
   - [Polymorphism](#3-polymorphism)
   - [Abstraction](#4-abstraction)
2. [Essential Comparison Topics](#essential-comparison-topics)
   - [Abstraction vs Encapsulation](#abstraction-vs-encapsulation)
   - [Method Overloading vs Method Overriding](#method-overloading-vs-method-overriding)
   - [Abstract Class vs Interface](#abstract-class-vs-interface)
   - [Composition vs Inheritance](#composition-vs-inheritance)
   - [Association vs Aggregation vs Composition](#association-vs-aggregation-vs-composition)
   - [Is C# Fully Object-Oriented?](#is-c-fully-object-oriented)
3. [Advanced Topics](#advanced-topics)
   - [SOLID Principles](#solid-principles)
   - [Dependency Injection](#dependency-injection)
   - [Common OOP Design Mistakes](#common-oop-design-mistakes)
   - [Real-World Enterprise Examples](#real-world-enterprise-examples)
4. [Final Cheat Sheet](#final-cheat-sheet)

---

# Core OOP Pillars

---

## 1. Encapsulation

### Definition
Encapsulation means **bundling data (fields) and the methods that operate on that data into a single unit (class), and restricting direct access to the internal state from outside**.

### Why It Matters
- Protects object integrity — no one can put the object into an invalid state.
- Hides implementation details — callers only see what they need to see.
- Makes code easier to maintain — change internals without breaking callers.

### Real-World Analogy
An ATM machine. You insert your card and press buttons (public interface), but you never touch the internal cash mechanism or software (private internals). The machine controls what you can and cannot do.

### Software Engineering Use Case
A `BankAccount` class. The balance should never be set directly from outside — it should only change through `Deposit()` and `Withdraw()` methods that enforce business rules (e.g., no negative balance).

### C# Code Example

```csharp
public class BankAccount
{
    private decimal _balance; // hidden from outside

    public decimal Balance => _balance; // read-only public view

    public void Deposit(decimal amount)
    {
        if (amount <= 0) throw new ArgumentException("Deposit must be positive.");
        _balance += amount;
    }

    public void Withdraw(decimal amount)
    {
        if (amount > _balance) throw new InvalidOperationException("Insufficient funds.");
        _balance -= amount;
    }
}

// Usage
var account = new BankAccount();
account.Deposit(500);
account.Withdraw(200);
Console.WriteLine(account.Balance); // 300

// account._balance = -9999; // ERROR — private, cannot access directly
```

### Common Interview Q&A

**Q: What is encapsulation?**
> Encapsulation is the practice of hiding an object's internal state and exposing only a controlled interface. In C#, this is done using access modifiers (`private`, `protected`, `public`) and properties.

**Q: What access modifiers does C# provide?**
> `public` — accessible everywhere. `private` — accessible only within the class. `protected` — accessible within the class and derived classes. `internal` — accessible within the same assembly. `protected internal` and `private protected` are combinations for more nuanced scenarios.

**Q: What is the difference between a field and a property?**
> A field is a raw variable stored in the class. A property is a controlled access point — it can have a getter and/or setter with custom logic. Properties are the standard way to expose data in C#.

**Q: Can you have encapsulation without private fields?**
> Technically yes, but it defeats the purpose. If all fields are public, nothing is hidden and any code can put your object in an invalid state.

### Common Mistakes
- Making fields `public` instead of exposing them through properties.
- Writing a property with a public getter and public setter but no validation logic — this is just a public field with extra syntax.
- Over-encapsulating: making everything private including things that genuinely need to be accessible, making the class hard to use.
- Confusing encapsulation with security — it is about design integrity, not access control security.

### Key Takeaways
- Use `private` fields and expose them via properties with logic.
- Encapsulation enforces business rules at the data boundary.
- It is the foundation of reliable, maintainable classes.

> **Summary:** Encapsulation = hide internal state + expose controlled interface. Use private fields, public properties, and validation in setters/methods. It prevents invalid object states.

---

## 2. Inheritance

### Definition
Inheritance allows a class (child/derived) to **acquire the fields, properties, and methods of another class (parent/base)**, promoting code reuse and establishing an "is-a" relationship.

### Why It Matters
- Eliminates duplicate code — common logic lives in one place.
- Establishes a clear type hierarchy.
- Enables polymorphism (derived classes can be used wherever the base class is expected).

### Real-World Analogy
A `Vehicle` is a general concept. A `Car` and a `Truck` are both vehicles — they inherit common traits (engine, wheels, speed) but each adds its own specifics (car has a trunk, truck has a cargo capacity). You do not redefine "engine" for every vehicle type.

### Software Engineering Use Case
An e-commerce system where `Product` is the base class. `PhysicalProduct` and `DigitalProduct` inherit from `Product` — they share `Name`, `Price`, and `GetDetails()`, but each adds its own behavior (`ShippingWeight` for physical, `DownloadUrl` for digital).

### C# Code Example

```csharp
public class Product
{
    public string Name { get; set; }
    public decimal Price { get; set; }

    public virtual string GetDetails()
    {
        return $"{Name} costs {Price:C}";
    }
}

public class PhysicalProduct : Product
{
    public double ShippingWeightKg { get; set; }

    public override string GetDetails()
    {
        return base.GetDetails() + $", Weight: {ShippingWeightKg}kg";
    }
}

public class DigitalProduct : Product
{
    public string DownloadUrl { get; set; }

    public override string GetDetails()
    {
        return base.GetDetails() + $", Download: {DownloadUrl}";
    }
}

// Usage
Product p1 = new PhysicalProduct { Name = "Book", Price = 29.99m, ShippingWeightKg = 0.5 };
Product p2 = new DigitalProduct { Name = "eBook", Price = 9.99m, DownloadUrl = "https://..." };

Console.WriteLine(p1.GetDetails());
Console.WriteLine(p2.GetDetails());
```

### Common Interview Q&A

**Q: What is inheritance and what problem does it solve?**
> Inheritance lets a derived class reuse and extend the behavior of a base class. It solves the problem of code duplication by centralizing shared logic and establishes an "is-a" relationship between types.

**Q: What is the difference between `virtual`, `override`, and `new` in C#?**
> `virtual` marks a base class method as overridable. `override` in the derived class replaces the base implementation polymorphically — the right version is called even through a base-type reference. `new` hides the base method but is NOT polymorphic — the version called depends on the reference type, not the actual object type.

**Q: Does C# support multiple inheritance?**
> C# does not support multiple class inheritance (a class cannot inherit from two classes). This avoids the "diamond problem." However, a class can implement multiple interfaces, which achieves similar flexibility without ambiguity.

**Q: When should you NOT use inheritance?**
> When the relationship is not truly "is-a." If you are inheriting just to reuse methods, prefer composition instead. Inheritance creates tight coupling — changes to the base class ripple down to all derived classes.

**Q: What does `sealed` do in C#?**
> `sealed` on a class prevents it from being inherited. `sealed` on an `override` method prevents further overriding in deeper derived classes.

### Common Mistakes
- Using inheritance for code reuse when composition is more appropriate (e.g., `Stack` inheriting from `List` — a stack is not a list).
- Forgetting to call `base.Method()` when the base class has important logic.
- Deep inheritance chains (more than 2-3 levels) — they become hard to understand and maintain.
- Confusing `new` with `override` — `new` does not achieve polymorphism.

### Key Takeaways
- Inheritance establishes "is-a" relationships and enables code reuse.
- Use `virtual`/`override` for polymorphic behavior; avoid `new` for hiding.
- Prefer shallow hierarchies and consider composition for flexible designs.

> **Summary:** Inheritance = derived class reuses and extends base class. Use it for genuine "is-a" relationships. Keep hierarchies shallow. Always prefer `override` over `new` for polymorphism.

---

## 3. Polymorphism

### Definition
Polymorphism means **one interface, many implementations** — the ability to treat objects of different types through a common base type, with each object responding differently to the same method call.

### Why It Matters
- Lets you write code that works with the base type but executes the right derived-type behavior automatically.
- Removes large `if/else` or `switch` chains based on type.
- Makes systems extensible — add a new type without changing existing code.

### Real-World Analogy
A payment terminal. You tap a credit card, debit card, or phone. The terminal calls `ProcessPayment()` on whatever device you present. Each device does it differently internally, but the terminal does not need to know which one it is.

### Software Engineering Use Case
A notification system with `EmailNotifier`, `SmsNotifier`, and `PushNotifier` all inheriting from `Notifier`. The notification dispatcher holds a `List<Notifier>` and calls `Send()` on each — it does not need to know the specific type.

### C# Code Example

```csharp
// Compile-time polymorphism (overloading)
public class Calculator
{
    public int Add(int a, int b) => a + b;
    public double Add(double a, double b) => a + b;
    public int Add(int a, int b, int c) => a + b + c;
}

// Runtime polymorphism (overriding via virtual/override)
public abstract class Notifier
{
    public abstract void Send(string message);
}

public class EmailNotifier : Notifier
{
    public override void Send(string message)
        => Console.WriteLine($"Email: {message}");
}

public class SmsNotifier : Notifier
{
    public override void Send(string message)
        => Console.WriteLine($"SMS: {message}");
}

public class PushNotifier : Notifier
{
    public override void Send(string message)
        => Console.WriteLine($"Push: {message}");
}

// Usage — the loop does not care about specific types
var notifiers = new List<Notifier>
{
    new EmailNotifier(),
    new SmsNotifier(),
    new PushNotifier()
};

foreach (var notifier in notifiers)
    notifier.Send("Your order has shipped!"); // each calls its own Send()
```

### Common Interview Q&A

**Q: What are the two types of polymorphism in C#?**
> **Compile-time (static) polymorphism** is achieved through method overloading — the correct method is selected at compile time based on the argument types and count. **Runtime (dynamic) polymorphism** is achieved through method overriding with `virtual`/`override` — the correct method is selected at runtime based on the actual object type.

**Q: What is the role of the `virtual` keyword?**
> It tells the C# runtime that this method can be overridden in derived classes, and that when called through a base-type reference, the derived version should be called. Without `virtual`, the base version is always called regardless of the actual object type.

**Q: What is dynamic dispatch?**
> Dynamic dispatch is the runtime mechanism that looks at the actual type of an object (not the reference type) to determine which method implementation to call. It is what makes runtime polymorphism work.

**Q: How does polymorphism support the Open/Closed Principle?**
> You can add a new `Notifier` subclass without modifying the dispatcher loop. The existing code is closed for modification but open for extension through new derived types.

### Common Mistakes
- Confusing method overloading (compile-time, same class) with method overriding (runtime, inheritance hierarchy).
- Using `new` keyword thinking it achieves polymorphism — it does not. Only `override` participates in runtime dispatch.
- Type-checking with `is` or casting with `as` inside polymorphic code — this defeats the purpose; let the override do the work.
- Forgetting `virtual` on the base method — without it, overrides do not dispatch polymorphically.

### Key Takeaways
- Polymorphism = same call, different behavior based on actual type.
- Overloading = compile-time; Overriding = runtime.
- Use it to remove type-checking conditionals and make code extensible.

> **Summary:** Polymorphism lets one interface serve many types. Overloading resolves at compile time; overriding resolves at runtime via dynamic dispatch. It is the mechanism behind extensible, flexible code.

---

## 4. Abstraction

### Definition
Abstraction means **exposing only what is relevant to the caller and hiding unnecessary complexity** — defining what an object does without specifying how it does it.

### Why It Matters
- Reduces cognitive load — callers work with simple, clear interfaces.
- Decouples "what" from "how" — you can swap implementations without changing callers.
- Enables design-first thinking — define contracts before writing implementations.

### Real-World Analogy
A car's steering wheel and pedals. You know that pressing the accelerator makes the car go faster. You do not need to understand fuel injection, combustion, or transmission gear ratios. The complex mechanics are abstracted away behind a simple interface.

### Software Engineering Use Case
A `IReportGenerator` interface used in a reporting service. The service calls `Generate()` without knowing whether the implementation produces PDF, Excel, or HTML. Switching from PDF to Excel means swapping one implementation — the service code does not change.

### C# Code Example

```csharp
// Abstraction via interface — defines WHAT, not HOW
public interface IReportGenerator
{
    byte[] Generate(ReportData data);
}

// Concrete implementations — define HOW
public class PdfReportGenerator : IReportGenerator
{
    public byte[] Generate(ReportData data)
    {
        // PDF-specific logic
        Console.WriteLine("Generating PDF report...");
        return new byte[0]; // simplified
    }
}

public class ExcelReportGenerator : IReportGenerator
{
    public byte[] Generate(ReportData data)
    {
        // Excel-specific logic
        Console.WriteLine("Generating Excel report...");
        return new byte[0]; // simplified
    }
}

// Caller works only with the abstraction
public class ReportService
{
    private readonly IReportGenerator _generator;

    public ReportService(IReportGenerator generator)
    {
        _generator = generator;
    }

    public void ExportReport(ReportData data)
    {
        var output = _generator.Generate(data); // doesn't know or care which type
        // save output...
    }
}

public class ReportData { public string Title { get; set; } }
```

### Common Interview Q&A

**Q: What is abstraction in OOP?**
> Abstraction is hiding implementation complexity and exposing only a clean, relevant interface. In C#, this is achieved using abstract classes and interfaces. The caller interacts with the contract, not the concrete implementation.

**Q: How do you achieve abstraction in C#?**
> Through **interfaces** (pure contract — only method signatures) and **abstract classes** (partial contract — can have both abstract methods and concrete implementations). Interfaces are the primary tool for abstraction in modern C#.

**Q: What is the difference between abstraction and encapsulation?**
> Abstraction hides complexity at the design level — it is about what an object does. Encapsulation hides internal state at the implementation level — it is about protecting data. You use abstraction to define a contract; you use encapsulation to protect the internals of an object implementing that contract.

**Q: Can you instantiate an abstract class?**
> No. An abstract class cannot be instantiated directly — it must be subclassed. It serves as a blueprint. If a derived class does not implement all abstract members, it must also be declared abstract.

### Common Mistakes
- Confusing abstraction with encapsulation (very common interview trap — see the comparison section).
- Creating interfaces with too many methods ("fat interfaces") — violates the Interface Segregation Principle.
- Making everything abstract when a simple concrete class suffices — over-engineering.
- Exposing implementation details in what should be an abstract interface (e.g., including database-specific method names in a general interface).

### Key Takeaways
- Abstraction = define what, hide how. Use interfaces and abstract classes.
- It decouples callers from implementations, enabling easy swapping.
- It is the foundation of the Dependency Inversion Principle and testable code.

> **Summary:** Abstraction hides complexity behind a clean interface. Use interfaces to define contracts and abstract classes for partial shared implementations. Callers depend on the "what", not the "how".

---

# Essential Comparison Topics

---

## Abstraction vs Encapsulation

These two are the most commonly confused OOP concepts in interviews. They are related but solve different problems.

| Aspect | Abstraction | Encapsulation |
|---|---|---|
| **Focus** | Hiding complexity | Hiding internal state/data |
| **Question answered** | What does this object do? | How is data protected? |
| **Achieved via** | Interfaces, abstract classes | Access modifiers, properties |
| **Level** | Design level | Implementation level |
| **Goal** | Define a contract | Protect object integrity |
| **Example** | `IPaymentProcessor` interface | `private decimal _balance` with validation |

**Simple way to remember:**
- Abstraction = hiding **complexity** (you see the steering wheel, not the engine).
- Encapsulation = hiding **data** (the engine is locked in the hood, you cannot touch it directly).

```csharp
// Abstraction — hides HOW payment works
public interface IPaymentProcessor
{
    bool ProcessPayment(decimal amount);
}

// Encapsulation — hides internal payment data
public class PayPalProcessor : IPaymentProcessor
{
    private string _apiKey;       // hidden
    private string _merchantId;   // hidden

    public PayPalProcessor(string apiKey, string merchantId)
    {
        _apiKey = apiKey;
        _merchantId = merchantId;
    }

    public bool ProcessPayment(decimal amount)
    {
        // internal logic hidden from caller
        return true;
    }
}
```

---

## Method Overloading vs Method Overriding

| Aspect | Method Overloading | Method Overriding |
|---|---|---|
| **What it is** | Multiple methods, same name, different parameters | Replacing base class method in derived class |
| **Resolved at** | Compile time | Runtime |
| **Inheritance required?** | No — same class | Yes — requires base/derived relationship |
| **Keyword needed** | None | `virtual` in base, `override` in derived |
| **Type of polymorphism** | Compile-time (static) | Runtime (dynamic) |
| **Return type** | Can differ if signature differs | Must match base |

```csharp
// Overloading — same class, different signatures
public class Logger
{
    public void Log(string message) => Console.WriteLine(message);
    public void Log(string message, LogLevel level) => Console.WriteLine($"[{level}] {message}");
    public void Log(Exception ex) => Console.WriteLine(ex.Message);
}

// Overriding — base/derived, runtime dispatch
public class BaseLogger
{
    public virtual void Log(string message) => Console.WriteLine($"Base: {message}");
}

public class FileLogger : BaseLogger
{
    public override void Log(string message) => Console.WriteLine($"File: {message}");
}

// Runtime dispatch demo
BaseLogger logger = new FileLogger();
logger.Log("test"); // prints "File: test" — FileLogger.Log() is called
```

> **Key interview point:** Overloading = same name, different parameters, same class. Overriding = same signature, different class, needs `virtual`/`override`.

---

## Abstract Class vs Interface

This is one of the most common interview questions in C# OOP.

| Aspect | Abstract Class | Interface |
|---|---|---|
| **Instantiation** | Cannot be instantiated | Cannot be instantiated |
| **Multiple inheritance** | A class can inherit only one | A class can implement many |
| **Method implementation** | Can have concrete methods | Default implementations allowed (C# 8+), but rare in practice |
| **Fields** | Can have instance fields | Cannot have instance fields |
| **Constructors** | Can have constructors | Cannot have constructors |
| **Access modifiers on members** | Any access modifier | All members are `public` by default |
| **Use when** | Shared base behavior + common state | Define a contract/capability |
| **"Is-a" vs "can-do"** | "Is-a" relationship | "Can-do" / capability |

```csharp
// Abstract class — shared state + partial implementation
public abstract class Animal
{
    public string Name { get; set; } // shared state

    public void Breathe() => Console.WriteLine("Breathing..."); // shared behavior

    public abstract void MakeSound(); // each animal defines its own
}

public class Dog : Animal
{
    public override void MakeSound() => Console.WriteLine("Woof!");
}

// Interface — pure capability contract
public interface ISwimmable
{
    void Swim();
}

public interface IFlyable
{
    void Fly();
}

// A class can implement multiple interfaces
public class Duck : Animal, ISwimmable, IFlyable
{
    public override void MakeSound() => Console.WriteLine("Quack!");
    public void Swim() => Console.WriteLine("Duck is swimming.");
    public void Fly() => Console.WriteLine("Duck is flying.");
}
```

**When to use which:**
- Use an **abstract class** when you have shared code/state and a natural "is-a" hierarchy.
- Use an **interface** when you want to define a capability that unrelated classes can share, or when you need multiple "inheritance."

> **Modern C# note:** With C# 8+, interfaces can have default method implementations. Despite this, the design guidance remains the same — use interfaces for contracts, abstract classes for shared implementations.

---

## Composition vs Inheritance

**Composition** means a class contains instances of other classes as members ("has-a"), rather than inheriting from them ("is-a").

| Aspect | Inheritance | Composition |
|---|---|---|
| **Relationship** | "is-a" | "has-a" |
| **Coupling** | Tight — base changes affect derived | Loose — components are independent |
| **Flexibility** | Less — fixed at compile time | More — components can be swapped |
| **Code reuse** | Through class hierarchy | Through contained objects |
| **Multiple behaviors** | Hard — single inheritance | Easy — include multiple components |
| **Testing** | Harder — tied to base class | Easier — inject mock components |

```csharp
// Inheritance approach — tight coupling
public class Logger
{
    public void Log(string message) => Console.WriteLine(message);
}

public class UserService : Logger // UserService "is-a" Logger? No — wrong.
{
    public void CreateUser(string name)
    {
        Log($"Creating user: {name}");
    }
}

// Composition approach — correct and flexible
public class UserService
{
    private readonly ILogger _logger; // "has-a" logger

    public UserService(ILogger logger)
    {
        _logger = logger;
    }

    public void CreateUser(string name)
    {
        _logger.LogInformation($"Creating user: {name}");
    }
}
```

> **Rule of thumb:** "Favor composition over inheritance" — a well-known principle from the Gang of Four. Use inheritance only for genuine "is-a" relationships. Use composition for code reuse.

---

## Association vs Aggregation vs Composition

All three describe relationships between classes, but they differ in ownership and lifecycle dependency.

| Concept | Relationship | Ownership | Lifecycle dependency | Example |
|---|---|---|---|---|
| **Association** | Uses / knows about | None | Independent | `Teacher` teaches `Student` |
| **Aggregation** | Has-a (weak) | Container references child | Independent — child can exist alone | `Department` has `Employees` |
| **Composition** | Has-a (strong) | Owner owns child | Dependent — child cannot exist without owner | `House` has `Rooms` |

```csharp
// Association — Teacher uses Student, no ownership
public class Teacher
{
    public void Grade(Student student) // just uses Student
    {
        Console.WriteLine($"Grading {student.Name}");
    }
}

// Aggregation — Department references Employees, but Employees exist independently
public class Department
{
    private List<Employee> _employees;

    public Department(List<Employee> employees)
    {
        _employees = employees; // employees passed in — they exist outside Department
    }
}

// Composition — House creates and owns Rooms; Rooms have no meaning without House
public class House
{
    private List<Room> _rooms;

    public House(int numberOfRooms)
    {
        _rooms = new List<Room>();
        for (int i = 0; i < numberOfRooms; i++)
            _rooms.Add(new Room()); // House creates rooms — they don't exist independently
    }
}
```

**Memory trick:**
- Association = "knows about" (loose)
- Aggregation = "has, but shares" (medium)
- Composition = "owns completely" (tight — destroy the owner, destroy the parts)

---

## Is C# Fully Object-Oriented?

**Short interview answer: No, C# is not purely/fully object-oriented.**

**Why not:**

| Reason | Explanation |
|---|---|
| **Primitive types** | `int`, `bool`, `char`, `double` are value types, not objects in the traditional OOP sense (though they are boxed to objects when needed). |
| **Static members** | Static methods and classes exist outside any object instance — pure OOP requires all behavior to belong to objects. |
| **Structs** | Value types in C# (`struct`) do not support full inheritance, breaking the OOP model. |
| **Global functions** | Top-level statements (C# 9+) allow code outside of any class. |

**What C# does have:**
- Classes, objects, interfaces, inheritance, polymorphism, encapsulation, abstraction — all core OOP features.
- Everything ultimately derives from `System.Object` (including primitive types through boxing).

**Strong interview answer:**
> "C# is an object-oriented language but not a purely object-oriented language. It supports all four OOP pillars, but it also includes value types, static constructs, and functional features like LINQ and lambdas that exist outside the pure OOP model. Languages like Smalltalk are considered purely OOP. C# is a multi-paradigm language — it blends OOP with functional and procedural styles for practicality."

---

# Advanced Topics

---

## SOLID Principles

SOLID is a set of five design principles that make OOP code maintainable, extensible, and testable. They are attributed to Robert C. Martin (Uncle Bob).

---

### S — Single Responsibility Principle (SRP)

**Definition:** A class should have one, and only one, reason to change.

| Bad | Good |
|---|---|
| One class handles business logic AND database AND email | Each concern lives in its own class |

```csharp
// BAD — UserService does too many things
public class UserService
{
    public void RegisterUser(string email, string password)
    {
        // Validate input
        if (string.IsNullOrEmpty(email)) throw new Exception("Invalid email");

        // Save to database
        var sql = $"INSERT INTO Users VALUES ('{email}', '{password}')";
        // execute sql...

        // Send welcome email
        var smtp = new SmtpClient();
        smtp.Send("welcome@app.com", email, "Welcome!", "Thanks for signing up.");
    }
}

// GOOD — each class has one responsibility
public class UserValidator
{
    public void Validate(string email, string password)
    {
        if (string.IsNullOrEmpty(email)) throw new ArgumentException("Invalid email");
    }
}

public class UserRepository
{
    public void Save(User user) { /* database logic */ }
}

public class EmailService
{
    public void SendWelcomeEmail(string email) { /* email logic */ }
}

public class UserService
{
    private readonly UserValidator _validator;
    private readonly UserRepository _repository;
    private readonly EmailService _emailService;

    public UserService(UserValidator validator, UserRepository repository, EmailService emailService)
    {
        _validator = validator;
        _repository = repository;
        _emailService = emailService;
    }

    public void RegisterUser(string email, string password)
    {
        _validator.Validate(email, password);
        _repository.Save(new User { Email = email });
        _emailService.SendWelcomeEmail(email);
    }
}
```

> **Interview tip:** "One reason to change" means one business concern. If a class changes when the database changes AND when the email template changes, it has multiple responsibilities.

---

### O — Open/Closed Principle (OCP)

**Definition:** Classes should be open for extension but closed for modification.

```csharp
// BAD — every new discount type requires modifying this class
public class DiscountCalculator
{
    public decimal Calculate(string customerType, decimal price)
    {
        if (customerType == "Regular") return price * 0.95m;
        if (customerType == "Premium") return price * 0.85m;
        if (customerType == "VIP") return price * 0.75m;
        return price; // new type? Must modify this method.
    }
}

// GOOD — add new discounts without touching existing code
public interface IDiscountStrategy
{
    decimal ApplyDiscount(decimal price);
}

public class RegularDiscount : IDiscountStrategy
{
    public decimal ApplyDiscount(decimal price) => price * 0.95m;
}

public class PremiumDiscount : IDiscountStrategy
{
    public decimal ApplyDiscount(decimal price) => price * 0.85m;
}

public class VipDiscount : IDiscountStrategy
{
    public decimal ApplyDiscount(decimal price) => price * 0.75m;
}

public class DiscountCalculator
{
    public decimal Calculate(IDiscountStrategy strategy, decimal price)
        => strategy.ApplyDiscount(price);
    // Adding a new discount = add a new class. No modification here.
}
```

> **Interview tip:** OCP is achieved through abstraction and polymorphism — the Strategy and Template Method patterns are classic OCP implementations.

---

### L — Liskov Substitution Principle (LSP)

**Definition:** Objects of a derived class must be replaceable for objects of the base class without breaking the program.

```csharp
// BAD — Square inheriting Rectangle violates LSP
public class Rectangle
{
    public virtual int Width { get; set; }
    public virtual int Height { get; set; }
    public int Area() => Width * Height;
}

public class Square : Rectangle
{
    public override int Width
    {
        set { base.Width = value; base.Height = value; } // keeps it square
    }
    public override int Height
    {
        set { base.Width = value; base.Height = value; }
    }
}

// This code breaks when a Square is substituted for a Rectangle:
void ResizeRectangle(Rectangle r)
{
    r.Width = 5;
    r.Height = 10;
    Console.WriteLine(r.Area()); // Expected 50, but Square gives 100!
}

// GOOD — use a common abstraction, not inheritance
public interface IShape
{
    int Area();
}

public class Rectangle : IShape
{
    public int Width { get; set; }
    public int Height { get; set; }
    public int Area() => Width * Height;
}

public class Square : IShape
{
    public int Side { get; set; }
    public int Area() => Side * Side;
}
```

> **Interview tip:** LSP violation often appears as a derived class throwing `NotImplementedException` for a method it inherits, or a method that makes no sense for the derived type (e.g., a `Bird.Fly()` when the bird is a `Penguin`).

---

### I — Interface Segregation Principle (ISP)

**Definition:** Clients should not be forced to depend on interfaces they do not use. Prefer small, focused interfaces over large, fat ones.

```csharp
// BAD — fat interface forces classes to implement methods they don't need
public interface IWorker
{
    void Work();
    void Eat();
    void Sleep();
}

public class Robot : IWorker
{
    public void Work() => Console.WriteLine("Robot working");
    public void Eat() => throw new NotImplementedException(); // robots don't eat!
    public void Sleep() => throw new NotImplementedException(); // robots don't sleep!
}

// GOOD — small, focused interfaces
public interface IWorkable { void Work(); }
public interface IEatable  { void Eat(); }
public interface ISleepable { void Sleep(); }

public class Human : IWorkable, IEatable, ISleepable
{
    public void Work() => Console.WriteLine("Human working");
    public void Eat() => Console.WriteLine("Human eating");
    public void Sleep() => Console.WriteLine("Human sleeping");
}

public class Robot : IWorkable
{
    public void Work() => Console.WriteLine("Robot working");
    // No forced implementation of irrelevant methods
}
```

> **Interview tip:** A sign of ISP violation is classes implementing interface methods with `throw new NotImplementedException()` or empty method bodies.

---

### D — Dependency Inversion Principle (DIP)

**Definition:** High-level modules should not depend on low-level modules. Both should depend on abstractions. Abstractions should not depend on details — details should depend on abstractions.

```csharp
// BAD — high-level OrderService directly depends on low-level SqlOrderRepository
public class SqlOrderRepository
{
    public void Save(Order order) { /* SQL logic */ }
}

public class OrderService
{
    private SqlOrderRepository _repo = new SqlOrderRepository(); // tight coupling!

    public void PlaceOrder(Order order) => _repo.Save(order);
}

// GOOD — both depend on the IOrderRepository abstraction
public interface IOrderRepository
{
    void Save(Order order);
}

public class SqlOrderRepository : IOrderRepository
{
    public void Save(Order order) { /* SQL logic */ }
}

public class MongoOrderRepository : IOrderRepository
{
    public void Save(Order order) { /* MongoDB logic */ }
}

public class OrderService
{
    private readonly IOrderRepository _repo;

    public OrderService(IOrderRepository repo) // depends on abstraction, not concrete type
    {
        _repo = repo;
    }

    public void PlaceOrder(Order order) => _repo.Save(order);
}
```

> **Interview tip:** DIP is the principle that Dependency Injection implements. You invert control by injecting dependencies from outside rather than creating them inside.

---

### SOLID — Quick Reference Table

| Principle | One-liner | Key tool in C# |
|---|---|---|
| **SRP** | One class, one responsibility | Split classes by concern |
| **OCP** | Extend without modifying | Interfaces + polymorphism |
| **LSP** | Subtypes must replace base types safely | Correct inheritance hierarchy |
| **ISP** | Small interfaces, not fat ones | Multiple focused interfaces |
| **DIP** | Depend on abstractions, not concretions | Interfaces + constructor injection |

> **Summary:** SOLID principles guide how to structure OOP code. SRP keeps classes focused. OCP and LSP govern extension and inheritance. ISP keeps contracts lean. DIP decouples layers through abstractions.

---

## Dependency Injection

### Definition
Dependency Injection (DI) is a technique where an object's dependencies are **provided from outside** rather than created inside the object. It is a practical implementation of the Dependency Inversion Principle.

### Relation to OOP
- Implements **DIP** — high-level classes depend on interfaces injected into them.
- Enables **Abstraction** — callers work with interfaces, not concrete types.
- Supports **Encapsulation** — internal dependencies are hidden behind constructor parameters.
- Improves **testability** — mock implementations can be injected in tests.

### Three Types of DI

| Type | How | Common in C# |
|---|---|---|
| **Constructor injection** | Dependencies passed via constructor | Yes — preferred |
| **Property injection** | Dependencies set via public property | Rarely — for optional dependencies |
| **Method injection** | Dependencies passed as method parameters | For single-use scenarios |

### C# Code Example

```csharp
// Interface (abstraction)
public interface IEmailService
{
    void SendEmail(string to, string subject, string body);
}

// Concrete implementation
public class SmtpEmailService : IEmailService
{
    public void SendEmail(string to, string subject, string body)
    {
        Console.WriteLine($"Sending SMTP email to {to}");
    }
}

// High-level class — constructor injection
public class OrderService
{
    private readonly IEmailService _emailService;
    private readonly IOrderRepository _orderRepository;

    // Dependencies injected — not created here
    public OrderService(IEmailService emailService, IOrderRepository orderRepository)
    {
        _emailService = emailService;
        _orderRepository = orderRepository;
    }

    public void PlaceOrder(Order order)
    {
        _orderRepository.Save(order);
        _emailService.SendEmail(order.CustomerEmail, "Order Confirmed", "Thank you!");
    }
}

// ASP.NET Core DI registration (Program.cs)
// builder.Services.AddScoped<IEmailService, SmtpEmailService>();
// builder.Services.AddScoped<IOrderRepository, SqlOrderRepository>();
// builder.Services.AddScoped<OrderService>();
// The DI container injects the right types automatically.
```

### DI in ASP.NET Core
ASP.NET Core has a built-in DI container. You register services with three lifetimes:

| Lifetime | Created | Best for |
|---|---|---|
| `AddSingleton` | Once per app lifetime | Shared state, configuration |
| `AddScoped` | Once per HTTP request | Database contexts, per-request services |
| `AddTransient` | Every time requested | Lightweight, stateless services |

> **Interview tip:** DI is not a design pattern itself — it is a technique. The patterns it uses are Strategy (swappable implementations) and IoC (Inversion of Control containers). The key benefit is testability — you can inject a mock `IEmailService` in unit tests without sending real emails.

---

## Common OOP Design Mistakes

| Mistake | Why it is a problem | Fix |
|---|---|---|
| **God class** | One class does everything — thousands of lines, multiple responsibilities | Apply SRP, split into focused classes |
| **Anemic domain model** | Classes have only data (getters/setters), all logic in services | Move behavior into the domain class |
| **Inheritance for code reuse only** | Creates wrong "is-a" relationships, tight coupling | Use composition instead |
| **Deep inheritance chains** | Hard to understand, fragile — base changes break everything | Keep hierarchies max 2-3 levels deep |
| **Violating LSP** | Derived class throws `NotImplementedException` or changes expected behavior | Redesign hierarchy or use interfaces |
| **Fat interfaces** | Classes forced to implement irrelevant methods | Split interfaces per ISP |
| **Exposing internal state** | Public fields or properties with no validation | Use private fields + controlled properties |
| **Creating dependencies inside classes** | Tight coupling, untestable | Inject dependencies via constructor |
| **Overusing static** | Static methods cannot be overridden or injected — fights OOP | Prefer instance methods; use static only for pure utilities |
| **Premature abstraction** | Interfaces/abstractions added before a second implementation exists | YAGNI — abstract when you have a second concrete need |

---

## Real-World Enterprise Examples

### 1. Repository Pattern (Data Access Abstraction)
Used in nearly every enterprise .NET application. Abstracts database access behind an interface.

```csharp
public interface IProductRepository
{
    Product GetById(int id);
    IEnumerable<Product> GetAll();
    void Add(Product product);
    void Update(Product product);
    void Delete(int id);
}

// EF Core implementation
public class EfProductRepository : IProductRepository
{
    private readonly AppDbContext _context;
    public EfProductRepository(AppDbContext context) => _context = context;

    public Product GetById(int id) => _context.Products.Find(id);
    public IEnumerable<Product> GetAll() => _context.Products.ToList();
    public void Add(Product product) { _context.Products.Add(product); _context.SaveChanges(); }
    public void Update(Product product) { _context.Products.Update(product); _context.SaveChanges(); }
    public void Delete(int id) { var p = GetById(id); if (p != null) { _context.Products.Remove(p); _context.SaveChanges(); } }
}
```

OOP principles applied: **Abstraction** (interface hides EF Core), **DIP** (services depend on `IProductRepository`), **Encapsulation** (database logic hidden inside repository).

---

### 2. Strategy Pattern in E-Commerce (OCP in Action)
Shipping cost calculation varies by carrier — add new carriers without modifying the checkout service.

```csharp
public interface IShippingStrategy
{
    decimal CalculateCost(Order order);
}

public class FedExShipping : IShippingStrategy
{
    public decimal CalculateCost(Order order) => order.WeightKg * 5.5m;
}

public class DhlShipping : IShippingStrategy
{
    public decimal CalculateCost(Order order) => order.WeightKg * 4.8m;
}

public class CheckoutService
{
    private readonly IShippingStrategy _shipping;
    public CheckoutService(IShippingStrategy shipping) => _shipping = shipping;

    public decimal GetTotal(Order order) => order.SubTotal + _shipping.CalculateCost(order);
}
```

---

### 3. ASP.NET Core Middleware (Chain of Responsibility + Abstraction)
Each middleware component handles one responsibility (logging, authentication, error handling) and passes control to the next.

```csharp
// Each middleware is an encapsulated, single-responsibility component
public class RequestLoggingMiddleware
{
    private readonly RequestDelegate _next;
    private readonly ILogger<RequestLoggingMiddleware> _logger;

    public RequestLoggingMiddleware(RequestDelegate next, ILogger<RequestLoggingMiddleware> logger)
    {
        _next = next;
        _logger = logger;
    }

    public async Task InvokeAsync(HttpContext context)
    {
        _logger.LogInformation($"Incoming: {context.Request.Method} {context.Request.Path}");
        await _next(context); // pass to next middleware
        _logger.LogInformation($"Outgoing: {context.Response.StatusCode}");
    }
}
```

OOP principles: **SRP** (each middleware does one thing), **Encapsulation** (internal logging details hidden), **DI** (logger injected via constructor).

---

### 4. Factory Pattern (Abstraction + Polymorphism)
Creates the correct object type based on input — callers never use `new` directly.

```csharp
public interface INotifier
{
    void Send(string message);
}

public class EmailNotifier : INotifier
{
    public void Send(string message) => Console.WriteLine($"Email: {message}");
}

public class SmsNotifier : INotifier
{
    public void Send(string message) => Console.WriteLine($"SMS: {message}");
}

public static class NotifierFactory
{
    public static INotifier Create(string type) => type switch
    {
        "email" => new EmailNotifier(),
        "sms"   => new SmsNotifier(),
        _ => throw new ArgumentException($"Unknown notifier type: {type}")
    };
}

// Usage
INotifier notifier = NotifierFactory.Create("email");
notifier.Send("Your package has arrived.");
```

---

# Final Cheat Sheet

> Use this for quick revision 10-15 minutes before an interview.

---

## OOP Pillars

| Pillar | One-liner | C# mechanism | Key interview point |
|---|---|---|---|
| **Encapsulation** | Hide data, expose interface | `private` fields + properties | Prevents invalid state; use validation in setters |
| **Inheritance** | Child reuses parent | `: BaseClass` | "is-a" only; keep hierarchies shallow |
| **Polymorphism** | One interface, many behaviors | `virtual`/`override` | Overloading = compile-time; Overriding = runtime |
| **Abstraction** | Hide complexity, show contract | Interfaces, abstract classes | Decouples "what" from "how" |

---

## Key Comparisons

| Topic | Side A | Side B |
|---|---|---|
| **Abstraction vs Encapsulation** | Hides complexity (design) | Hides data (implementation) |
| **Overloading vs Overriding** | Same class, different params, compile-time | Inheritance, same signature, runtime |
| **Abstract class vs Interface** | One per class, can have state/code | Many per class, pure contract |
| **Composition vs Inheritance** | "has-a", loose, flexible | "is-a", tight, less flexible |
| **Aggregation vs Composition** | Child can exist independently | Child cannot exist without parent |

---

## SOLID — One Line Each

| Principle | Rule |
|---|---|
| **S**RP | One class, one reason to change |
| **O**CP | Extend via new code, don't modify existing |
| **L**SP | Subtypes must not break base-type expectations |
| **I**SP | Many small interfaces over one large one |
| **D**IP | Depend on abstractions, inject concretes |

---

## Access Modifiers

| Modifier | Accessible from |
|---|---|
| `public` | Anywhere |
| `private` | Same class only |
| `protected` | Same class + derived classes |
| `internal` | Same assembly |
| `protected internal` | Same assembly OR derived classes |
| `private protected` | Same class AND derived classes in same assembly |

---

## DI Service Lifetimes (ASP.NET Core)

| Lifetime | When created | Use for |
|---|---|---|
| `Singleton` | Once, reused always | Config, caches, stateless services |
| `Scoped` | Once per HTTP request | DbContext, per-request services |
| `Transient` | Every time requested | Lightweight, stateless utilities |

---

## Common Interview Answers — Quick Reference

| Question | Key points in your answer |
|---|---|
| Is C# fully OOP? | No — has primitives, statics, structs, top-level code. Multi-paradigm. |
| Abstraction vs Encapsulation? | Abstraction = hide complexity. Encapsulation = hide data. Different levels. |
| When to use interface vs abstract class? | Interface for contract/capability. Abstract class for shared state + partial impl. |
| Composition vs Inheritance? | Prefer composition. Use inheritance only for true "is-a". |
| What is DI and why? | Inject dependencies from outside; enables testing, loose coupling, DIP. |
| What is LSP violation? | Derived class breaks expected behavior — e.g., `NotImplementedException`. |

---

## Design Pattern Quick Map

| Pattern | OOP principle | Use case |
|---|---|---|
| **Repository** | Abstraction, DIP | Data access layer |
| **Strategy** | OCP, Polymorphism | Swappable algorithms |
| **Factory** | Abstraction, Polymorphism | Object creation without `new` |
| **Decorator** | OCP, Composition | Add behavior without modifying |
| **Observer** | Abstraction, SRP | Event-driven, notifications |

---

*End of guide. Good luck in your interviews.*



















SOLID principles are basically rules for writing clean, scalable, interview-ready OOP code. Interviewers love them because they directly test whether you think in design, not just syntax.

I’ll break each principle into:

Core idea (simple)
Real-world analogy
C# code example
Common interview scenario
Relation with OOP concepts
🔷 S — Single Responsibility Principle (SRP)
💡 Idea

A class should have only one reason to change.

🌍 Real-world example

A Restaurant system:

Bad design:

One “RestaurantManager” class handles:
billing
cooking
inventory
delivery

If billing changes → whole class breaks.

Good design:

BillingService
CookingService
InventoryService

Each has ONE responsibility.

💻 C# Example

❌ Bad:

class Invoice
{
    public void CalculateTotal() {}
    public void PrintInvoice() {}
    public void SaveToDatabase() {}
}

✔ Good:

class InvoiceCalculator
{
    public double CalculateTotal() => 1000;
}

class InvoicePrinter
{
    public void Print() {}
}

class InvoiceRepository
{
    public void Save() {}
}
🧠 Interview scenario

“Design a food delivery system”

If you put:

order logic
payment logic
notification logic

all in one class → ❌ SRP violation

🔗 Relation
Strongly connected to OOP encapsulation
Helps avoid God classes
🔷 O — Open/Closed Principle (OCP)
💡 Idea

Software should be:

Open for extension
Closed for modification
🌍 Real-world example

Payment system:

Initially: Cash only
Later: Add Card, JazzCash, Stripe

You should NOT modify existing payment code every time.

💻 C# Example

❌ Bad:

class Payment
{
    public void Pay(string type)
    {
        if(type == "cash") {}
        else if(type == "card") {}
    }
}

✔ Good (polymorphism):

interface IPayment
{
    void Pay();
}

class CashPayment : IPayment
{
    public void Pay() {}
}

class CardPayment : IPayment
{
    public void Pay() {}
}

Now we EXTEND without modifying.

🧠 Interview scenario

“Add new payment method in system”

If you modify existing logic → bad design
If you add new class → good OCP

🔗 Relation
Uses polymorphism
Uses interfaces / abstraction
🔷 L — Liskov Substitution Principle (LSP)
💡 Idea

Child class must be replaceable with parent class without breaking system.

🌍 Real-world example

If “Bird” can fly:

Sparrow → OK
Penguin → ❌ breaks rule

So Penguin should NOT inherit Bird (bad design)

💻 C# Example

❌ Bad:

class Bird
{
    public virtual void Fly() {}
}

class Penguin : Bird
{
    public override void Fly()
    {
        throw new Exception("Cannot fly");
    }
}

✔ Good design:

class Bird {}

interface IFlyable
{
    void Fly();
}

class Sparrow : Bird, IFlyable
{
    public void Fly() {}
}

class Penguin : Bird {}
🧠 Interview scenario

“Inheritance hierarchy of animals”

If child breaks parent behavior → LSP violation

🔗 Relation
Deeply connected with inheritance
Encourages correct abstraction design
🔷 I — Interface Segregation Principle (ISP)
💡 Idea

Do NOT force classes to implement unused methods.

🌍 Real-world example

Worker system:

HumanWorker → eats, works, sleeps
RobotWorker → only works

If both implement same interface → problem.

💻 C# Example

❌ Bad:

interface IWorker
{
    void Work();
    void Eat();
}

Robot must implement Eat ❌

✔ Good:

interface IWorkable
{
    void Work();
}

interface IEatable
{
    void Eat();
}

Now:

HumanWorker implements both
RobotWorker implements only Work
🧠 Interview scenario

“Design employee system”

If one class is forced to implement irrelevant methods → ISP violation

🔗 Relation
Improves interface design
Reduces tight coupling
🔷 D — Dependency Inversion Principle (DIP)
💡 Idea

Depend on abstractions, not concrete classes

🌍 Real-world example

Car engine system:

Car should depend on “Engine interface”
Not DieselEngine / PetrolEngine directly
💻 C# Example

❌ Bad:

class DieselEngine {}

class Car
{
    DieselEngine engine = new DieselEngine();
}

✔ Good:

interface IEngine
{
    void Start();
}

class DieselEngine : IEngine
{
    public void Start() {}
}

class Car
{
    private IEngine engine;

    public Car(IEngine engine)
    {
        this.engine = engine;
    }
}
🧠 Interview scenario

“Design scalable backend system”

If you hardcode dependencies → bad
If you inject interfaces → good

🔗 Relation
Uses dependency injection
Uses abstraction heavily
🔥 HOW SOLID WORKS TOGETHER

Think like this:

Principle	OOP Concept
SRP	Encapsulation
OCP	Polymorphism
LSP	Inheritance correctness
ISP	Interface design
DIP	Abstraction + DI
🧠 INTERVIEW SCENARIOS (VERY IMPORTANT)
1. “Design a Food Delivery App”

You should say:

SRP → separate Order, Payment, Notification
OCP → new payment methods added via new classes
DIP → services depend on interfaces
ISP → small interfaces (Payable, Deliverable)
LSP → inheritance used correctly for roles
2. “Design Banking System”
SRP → AccountService, TransactionService
OCP → new account types
DIP → Bank depends on IAccountRepository
ISP → separate interfaces for ATM, Online banking
LSP → SavingsAccount replaces Account safely
3. “Design Uber system”
SRP → RideService, PaymentService
OCP → new vehicle types
DIP → Driver depends on IVehicle
ISP → separate interfaces for Ride, Payment
LSP → Car/Bike replace Vehicle correctly
🔥 COMMON INTERVIEW QUESTIONS
1. Why SOLID?

Answer:

scalable code
maintainability
testability
reduces tight coupling
2. Which principle is most important?

Depends, but often:

DIP + OCP are most critical in real systems
3. Difference between SRP and OCP?
SRP → one reason to change
OCP → extend without modification
🚀 FINAL MENTAL MODEL

If you remember ONE thing:

SOLID = How to structure OOP systems so they don’t break when requirements change
