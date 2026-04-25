# Object-Oriented Design in C# — Fresh Graduate Interview Guide

> **How to use this guide:** Read Section 1 first to understand the big picture. Study Sections 2–8 thoroughly — these are tested most in interviews. Use Sections 9–12 for practical design practice. Revise using the final cheat sheet the day before your interview.

---

## Table of Contents

1. [OOD Fundamentals](#1-ood-fundamentals)
2. [SOLID Principles](#2-solid-principles)
3. [Relationships Between Classes](#3-relationships-between-classes)
4. [Composition vs Inheritance](#4-composition-vs-inheritance)
5. [Dependency Injection](#5-dependency-injection)
6. [Interface-Based Design](#6-interface-based-design)
7. [Low-Level Design Principles](#7-low-level-design-principles)
8. [Design Patterns for Fresh Graduates](#8-design-patterns-for-fresh-graduates)
9. [Basic UML Diagrams](#9-basic-uml-diagrams)
10. [OOD in Real Projects](#10-ood-in-real-projects)
11. [Common Interview Questions](#11-common-interview-questions)
12. [Common Mistakes](#12-common-mistakes)
13. [Final Cheat Sheet](#final-cheat-sheet)

---

## 1. OOD Fundamentals

### What is Object-Oriented Design?

Object-Oriented Design (OOD) is the process of **planning how to structure your software using classes, objects, and their relationships** before you write any code.

- OOD answers the question: *"Which classes do I need, what should each class be responsible for, and how should they interact?"*
- It is a design activity — done on paper or in a diagram — before touching the keyboard.
- Good OOD leads to code that is easy to read, change, test, and extend.

### OOP vs OOD — What is the Difference?

This is a common interview question that confuses many fresh graduates.

| Aspect | OOP | OOD |
|---|---|---|
| **What it is** | A programming paradigm | A design process |
| **Focus** | Language features (classes, inheritance, polymorphism) | Structure of a system before coding |
| **Output** | Working code | Class diagrams, design decisions |
| **When used** | During implementation | Before implementation |
| **Tools** | C#, Java, Python | UML diagrams, design principles |
| **Question it answers** | "How do I write the code?" | "What classes do I need and how do they relate?" |

**Simple way to remember:**
- OOP = the tools (features of the language).
- OOD = how you use those tools wisely to build a well-structured system.

**Example:** Before building a library system, OOD helps you decide:
- You need a `Book` class, a `Member` class, a `Loan` class.
- A `Member` can borrow many `Book` objects.
- `Loan` tracks the relationship between `Member` and `Book`.

Then OOP (C# code) implements those decisions.

### Why OOD Matters

Without proper OOD:
- Code becomes a tangled mess where everything depends on everything.
- A small change in one place breaks five other places.
- Adding a new feature takes weeks instead of hours.
- Testing individual parts becomes impossible.

With good OOD:
- Each class has one clear job.
- Classes are loosely connected — change one without fear.
- New features fit in neatly without rewriting existing code.
- Each class can be tested independently.

### How OOD Improves Software Quality

| Quality | How OOD helps |
|---|---|
| **Maintainability** | Each class is small and focused — easy to find and fix bugs |
| **Scalability** | New features are added by extending, not rewriting |
| **Testability** | Small, focused classes with clear interfaces are easy to unit test |
| **Readability** | Well-named classes and clear responsibilities make code self-documenting |
| **Reusability** | Well-designed classes can be reused in other projects |

> **Summary:** OOD is the planning step before coding. OOP is the implementation. Good OOD means your classes are small, focused, loosely connected, and easy to change. It improves maintainability, scalability, and testability.

---

## 2. SOLID Principles

SOLID is a set of five design principles that guide you toward clean, maintainable OOD. These are among the most frequently asked topics in fresh graduate interviews.

**Memory trick:** **S**ome **O**ld **L**egend **I**nvented **D**esign.

---

### S — Single Responsibility Principle (SRP)

**Definition:** A class should have one, and only one, reason to change.

**Problem it solves:** When a class does too many things, changing one feature risks breaking others. A class that handles both business logic and database access will need to change when either the business rules or the database changes — two reasons to change.

**Bad example:**

```csharp
// BAD — UserService does three different things
public class UserService
{
    public void RegisterUser(string email, string password)
    {
        // 1. Validate input
        if (string.IsNullOrEmpty(email))
            throw new ArgumentException("Invalid email");

        // 2. Save to database (database concern mixed in)
        var sql = $"INSERT INTO Users VALUES ('{email}', '{password}')";
        // ... run sql

        // 3. Send welcome email (email concern mixed in)
        Console.WriteLine($"Sending welcome email to {email}");
    }
}
```

**Good example:**

```csharp
// GOOD — each class has exactly one job

public class UserValidator
{
    public void Validate(string email, string password)
    {
        if (string.IsNullOrEmpty(email))
            throw new ArgumentException("Email is required.");
    }
}

public class UserRepository
{
    public void Save(string email, string hashedPassword)
    {
        // only database logic here
        Console.WriteLine($"Saving user {email} to database.");
    }
}

public class EmailService
{
    public void SendWelcome(string email)
    {
        // only email logic here
        Console.WriteLine($"Sending welcome email to {email}.");
    }
}

public class UserService
{
    private readonly UserValidator _validator;
    private readonly UserRepository _repository;
    private readonly EmailService _emailService;

    public UserService(UserValidator validator, UserRepository repository, EmailService emailService)
    {
        _validator   = validator;
        _repository  = repository;
        _emailService = emailService;
    }

    public void RegisterUser(string email, string password)
    {
        _validator.Validate(email, password);
        _repository.Save(email, password);
        _emailService.SendWelcome(email);
    }
}
```

**Real-world use case:** In a Pakistani e-commerce app, separating `OrderValidator`, `OrderRepository`, and `OrderNotifier` means the email team can change notification logic without touching the database team's code.

**Common interview Q&A:**

**Q: What does "one reason to change" mean?**
> It means one business concern. If the class changes because of a database change AND because of an email template change, it has two responsibilities and violates SRP.

**Q: How small should a class be?**
> Small enough to be described in one sentence without using "and." If you say "this class validates users AND saves them AND sends emails," it violates SRP.

---

### O — Open/Closed Principle (OCP)

**Definition:** Classes should be open for extension but closed for modification.

**Problem it solves:** When you add a new feature by modifying existing, working code, you risk introducing bugs. OCP means you add new code (a new class) rather than changing old code.

**Bad example:**

```csharp
// BAD — every new discount type requires editing this class
public class DiscountCalculator
{
    public decimal Calculate(string customerType, decimal price)
    {
        if (customerType == "Regular")  return price * 0.95m;
        if (customerType == "Premium")  return price * 0.85m;
        // Adding VIP? Must edit this method — risky!
        return price;
    }
}
```

**Good example:**

```csharp
// GOOD — add new discount by adding a new class, not editing existing code

public interface IDiscountStrategy
{
    decimal Apply(decimal price);
}

public class RegularDiscount : IDiscountStrategy
{
    public decimal Apply(decimal price) => price * 0.95m;
}

public class PremiumDiscount : IDiscountStrategy
{
    public decimal Apply(decimal price) => price * 0.85m;
}

// Adding VIP? Just add this class — no existing code touched.
public class VipDiscount : IDiscountStrategy
{
    public decimal Apply(decimal price) => price * 0.75m;
}

public class DiscountCalculator
{
    public decimal Calculate(IDiscountStrategy strategy, decimal price)
        => strategy.Apply(price);
}
```

**Real-world use case:** A payment system supports PayPal today. Tomorrow the client wants Stripe. With OCP, you add `StripePaymentProcessor` without touching `PayPalPaymentProcessor` or the checkout code.

**Common interview Q&A:**

**Q: How do you extend a class without modifying it?**
> Through interfaces and inheritance. Define an interface for the behavior, and add new behavior by writing a new class that implements the interface. The caller depends on the interface — it does not need to change.

**Q: Is it ever okay to modify an existing class?**
> Yes — for bug fixes, or when the class was not designed for extension yet. OCP is a guide, not an absolute rule. The key is to not modify well-tested, stable production code just to add a feature.

---

### L — Liskov Substitution Principle (LSP)

**Definition:** Objects of a derived class must be usable wherever objects of the base class are expected, without breaking the program.

**Problem it solves:** A derived class that changes or breaks the expected behavior of the base class makes inheritance dangerous and unpredictable.

**Bad example:**

```csharp
// BAD — Square breaks the behavior of Rectangle
public class Rectangle
{
    public virtual int Width  { get; set; }
    public virtual int Height { get; set; }
    public int Area() => Width * Height;
}

public class Square : Rectangle
{
    public override int Width  { set { base.Width  = value; base.Height = value; } }
    public override int Height { set { base.Width  = value; base.Height = value; } }
}

// This function expects any Rectangle to work correctly:
void TestRectangle(Rectangle r)
{
    r.Width  = 4;
    r.Height = 5;
    Console.WriteLine(r.Area()); // Expected: 20, but Square gives 25!
}
```

**Good example:**

```csharp
// GOOD — use a common interface instead of wrong inheritance

public interface IShape
{
    int Area();
}

public class Rectangle : IShape
{
    public int Width  { get; set; }
    public int Height { get; set; }
    public int Area() => Width * Height;
}

public class Square : IShape
{
    public int Side { get; set; }
    public int Area() => Side * Side;
}

// Now both can be used as IShape without surprises
void PrintArea(IShape shape) => Console.WriteLine(shape.Area());
```

**Real-world use case:** A `Bird` base class has a `Fly()` method. A `Penguin` class extends `Bird` — but penguins cannot fly. Calling `Fly()` on a `Penguin` either throws an exception or does nothing — both break LSP. Solution: separate `IFlyable` interface; only flying birds implement it.

**Common interview Q&A:**

**Q: How do you know if LSP is violated?**
> Look for these signs: a derived class throws `NotImplementedException` for an inherited method, or a method behaves unexpectedly when you substitute a derived object for the base. If you need `is` checks ("if this is a Penguin, skip Fly"), LSP is violated.

**Q: Does LSP only apply to inheritance?**
> Primarily yes, but it also applies to interface implementations. If an interface says `GetData()` returns a non-null value and your implementation can return null, you are violating the contract — breaking LSP for the interface.

---

### I — Interface Segregation Principle (ISP)

**Definition:** A class should not be forced to implement interfaces it does not use. Prefer small, focused interfaces over large, fat ones.

**Problem it solves:** A large interface forces every implementing class to write methods it does not need — leading to empty implementations or exceptions thrown for irrelevant methods.

**Bad example:**

```csharp
// BAD — fat interface forces Robot to implement irrelevant methods
public interface IWorker
{
    void Work();
    void Eat();
    void Sleep();
}

public class Robot : IWorker
{
    public void Work()  => Console.WriteLine("Robot working");
    public void Eat()   => throw new NotImplementedException(); // robots don't eat!
    public void Sleep() => throw new NotImplementedException(); // robots don't sleep!
}
```

**Good example:**

```csharp
// GOOD — small focused interfaces

public interface IWorkable  { void Work(); }
public interface IEatable   { void Eat(); }
public interface ISleepable { void Sleep(); }

public class Human : IWorkable, IEatable, ISleepable
{
    public void Work()  => Console.WriteLine("Human working");
    public void Eat()   => Console.WriteLine("Human eating");
    public void Sleep() => Console.WriteLine("Human sleeping");
}

public class Robot : IWorkable
{
    public void Work() => Console.WriteLine("Robot working");
    // No forced implementation of irrelevant methods
}
```

**Real-world use case:** An `IRepository<T>` interface with `Read()`, `Write()`, `Delete()`, and `BulkImport()`. A read-only reporting service only needs `Read()`. ISP says: split into `IReadRepository` and `IWriteRepository` so the reporting service only implements what it needs.

**Common interview Q&A:**

**Q: How do you spot an ISP violation?**
> Look for `throw new NotImplementedException()` or empty method bodies in interface implementations. If a class implements an interface but half the methods are empty or throw, the interface is too fat.

**Q: Is it okay to have many small interfaces?**
> Yes. Small, focused interfaces are better than large ones. A class can implement multiple small interfaces. The goal is that every interface member is relevant to every implementing class.

---

### D — Dependency Inversion Principle (DIP)

**Definition:** High-level classes should not depend on low-level classes. Both should depend on abstractions (interfaces).

**Problem it solves:** When a high-level class directly creates or references a low-level class, they become tightly coupled. Changing the low-level class forces changes in the high-level class too.

**Bad example:**

```csharp
// BAD — OrderService is tightly coupled to SqlOrderRepository
public class SqlOrderRepository
{
    public void Save(string order) => Console.WriteLine($"SQL: Saving {order}");
}

public class OrderService
{
    private SqlOrderRepository _repo = new SqlOrderRepository(); // tight coupling!

    public void PlaceOrder(string order) => _repo.Save(order);
}
// Want to switch to MongoDB? Must change OrderService.
```

**Good example:**

```csharp
// GOOD — both depend on the IOrderRepository abstraction

public interface IOrderRepository
{
    void Save(string order);
}

public class SqlOrderRepository : IOrderRepository
{
    public void Save(string order) => Console.WriteLine($"SQL: Saving {order}");
}

public class MongoOrderRepository : IOrderRepository
{
    public void Save(string order) => Console.WriteLine($"MongoDB: Saving {order}");
}

public class OrderService
{
    private readonly IOrderRepository _repo;

    public OrderService(IOrderRepository repo) // depends on abstraction
    {
        _repo = repo;
    }

    public void PlaceOrder(string order) => _repo.Save(order);
}

// Switch database by changing one line:
var service = new OrderService(new MongoOrderRepository());
```

**Real-world use case:** Every service class in an ASP.NET Core application depends on an interface, not a concrete class. This is why DI containers work — they inject the right concrete implementation at runtime.

**Common interview Q&A:**

**Q: What is the difference between DIP and Dependency Injection?**
> DIP is the principle — it says depend on abstractions. Dependency Injection is the technique that implements DIP — instead of creating dependencies inside a class (`new SqlRepo()`), you inject them from outside via constructor. DIP tells you what to do; DI tells you how to do it.

**Q: Why is DIP important for unit testing?**
> When `OrderService` depends on `IOrderRepository` (an interface), you can inject a fake/mock repository in tests — no real database needed. If it depended on `SqlOrderRepository` directly, you could not test without a database connection.

> **Summary — SOLID:** SRP (one reason to change), OCP (extend without modifying), LSP (derived types must not break base behavior), ISP (small focused interfaces), DIP (depend on abstractions). These five principles work together to produce clean, testable, maintainable designs.

---

## 3. Relationships Between Classes

Understanding how classes relate to each other is one of the most tested topics in junior-level design interviews.

---

### Association

**Definition:** A general relationship where one class uses or knows about another, but neither owns the other. Both can exist independently.

**UML notation:** `A ——— B` (plain line, no arrowhead or diamond)

**Real-world analogy:** A `Teacher` teaches `Students`. The teacher and students exist independently — removing the teacher does not delete the students.

**C# example:**

```csharp
public class Student
{
    public string Name { get; set; }
}

public class Teacher
{
    public string Name { get; set; }

    // Association — Teacher uses Student, but doesn't own it
    public void Grade(Student student)
    {
        Console.WriteLine($"{Name} is grading {student.Name}");
    }
}

// Both exist independently
var student = new Student { Name = "Ali" };
var teacher = new Teacher { Name = "Sir Ahmed" };
teacher.Grade(student);
```

**When to use:** When two classes interact but neither controls the other's lifecycle.

---

### Aggregation

**Definition:** A "has-a" relationship where one class (the whole) contains references to other class objects (the parts), but the parts can exist independently of the whole.

**UML notation:** `Whole ◇——— Part` (open diamond at the whole side)

**Real-world analogy:** A `Department` has `Employees`. If the department is dissolved, the employees still exist — they can be moved to another department.

**C# example:**

```csharp
public class Employee
{
    public string Name { get; set; }
}

public class Department
{
    public string Name { get; set; }

    // Aggregation — employees are passed in, they exist outside Department
    private List<Employee> _employees;

    public Department(List<Employee> employees)
    {
        _employees = employees; // Department holds references, doesn't create them
    }

    public void ListEmployees()
    {
        foreach (var emp in _employees)
            Console.WriteLine(emp.Name);
    }
}

// Employees exist independently
var employees = new List<Employee>
{
    new Employee { Name = "Bilal" },
    new Employee { Name = "Sara" }
};
var dept = new Department(employees);
// employees list still exists even if dept is deleted
```

**When to use:** When a container class holds items, but those items make sense on their own.

---

### Composition

**Definition:** A strong "has-a" relationship where the parts are created by and belong entirely to the whole. If the whole is destroyed, the parts are destroyed too.

**UML notation:** `Whole ◆——— Part` (filled diamond at the whole side)

**Real-world analogy:** A `House` has `Rooms`. If you demolish the house, the rooms cease to exist — they have no meaning without the house.

**C# example:**

```csharp
public class Room
{
    public string Name { get; set; }
    public Room(string name) => Name = name;
}

public class House
{
    // Composition — House creates and owns Rooms
    private List<Room> _rooms;

    public House(int numberOfRooms)
    {
        _rooms = new List<Room>();
        for (int i = 1; i <= numberOfRooms; i++)
            _rooms.Add(new Room($"Room {i}")); // House creates rooms internally
    }

    public void ShowRooms()
    {
        foreach (var room in _rooms)
            Console.WriteLine(room.Name);
    }
}

var house = new House(3);
house.ShowRooms();
// When house goes out of scope, rooms are gone too
```

**When to use:** When the parts have no meaningful existence without the whole (e.g., `Order` and `OrderLineItem`, `Document` and `Paragraph`).

---

### Dependency

**Definition:** The weakest relationship — a class uses another class temporarily (usually as a method parameter or local variable), but does not hold a long-term reference to it.

**UML notation:** `A - - -> B` (dashed arrow)

**Real-world analogy:** A carpenter `uses` a `Hammer` to do a job. The carpenter does not own or store the hammer permanently — they use it for the duration of the task.

**C# example:**

```csharp
public class EmailMessage
{
    public string To      { get; set; }
    public string Subject { get; set; }
    public string Body    { get; set; }
}

public class EmailSender
{
    // Dependency — EmailMessage is used temporarily as a method parameter
    public void Send(EmailMessage message)
    {
        Console.WriteLine($"Sending email to {message.To}: {message.Subject}");
    }
}
```

**When to use:** When a class briefly uses another class in a method — as a parameter, return type, or local variable — but does not store it as a field.

---

### Inheritance

**Definition:** An "is-a" relationship where a child class derives all properties and behaviors of a parent class and can add or override them.

**UML notation:** `Child ——▷ Parent` (solid line with open arrowhead pointing to parent)

**Real-world analogy:** A `Dog` is an `Animal`. Every dog has animal traits (breathes, has a name) plus dog-specific traits (barks, fetches).

**C# example:**

```csharp
public class Animal
{
    public string Name { get; set; }
    public virtual void Speak() => Console.WriteLine($"{Name} makes a sound.");
}

public class Dog : Animal
{
    public override void Speak() => Console.WriteLine($"{Name} says: Woof!");
}

public class Cat : Animal
{
    public override void Speak() => Console.WriteLine($"{Name} says: Meow!");
}

Animal dog = new Dog { Name = "Bruno" };
Animal cat = new Cat { Name = "Whiskers" };
dog.Speak(); // Bruno says: Woof!
cat.Speak(); // Whiskers says: Meow!
```

**When to use:** Only for genuine "is-a" relationships where the child truly IS a type of the parent. Do not use inheritance just to reuse code — use composition for that.

---

### Relationships Summary Table

| Relationship | Strength | Ownership | Lifecycle | UML Symbol | Example |
|---|---|---|---|---|---|
| **Association** | Weak | None | Independent | `A ——— B` | Teacher — Student |
| **Dependency** | Weakest | None | Independent (temporary) | `A - - -> B` | EmailSender uses EmailMessage |
| **Aggregation** | Medium | Whole references parts | Parts exist independently | `◇——— Part` | Department — Employee |
| **Composition** | Strong | Whole owns parts | Parts destroyed with whole | `◆——— Part` | House — Room |
| **Inheritance** | Tight | Child extends parent | Child depends on parent | `Child ——▷ Parent` | Dog — Animal |

> **Summary:** Association (knows about), Dependency (uses temporarily), Aggregation (has-a, parts independent), Composition (has-a, parts die with whole), Inheritance (is-a). Master the differences — interviewers test these constantly.

---

## 4. Composition vs Inheritance

This is one of the most important topics for fresh graduate interviews. A classic GoF principle states: **"Favor composition over inheritance."**

### Detailed Comparison

| Aspect | Inheritance | Composition |
|---|---|---|
| **Relationship** | "Is-a" | "Has-a" |
| **Coupling** | Tight — base class changes affect all children | Loose — components are independent |
| **Flexibility** | Fixed at compile time | Can be changed at runtime |
| **Code reuse** | Through the class hierarchy | Through contained objects |
| **Multiple behaviors** | Hard — C# has single inheritance only | Easy — include multiple components |
| **Testing** | Harder — child depends on base class | Easier — inject mock components |
| **Encapsulation** | Base class internals exposed to children | Components are hidden behind interfaces |
| **When to use** | True "is-a" relationships | Code reuse, flexible behavior |

### When to Prefer Composition

Use composition when:
- You want to reuse code but the relationship is not truly "is-a."
- You need to swap behavior at runtime.
- The behavior might vary independently of the type.
- You want to combine multiple behaviors from different sources.

```csharp
// WRONG — using inheritance just to reuse Log()
public class Logger
{
    public void Log(string msg) => Console.WriteLine($"[LOG] {msg}");
}

public class UserService : Logger // UserService "is-a" Logger? No — wrong!
{
    public void CreateUser(string name) => Log($"Creating {name}");
}

// CORRECT — use composition
public interface ILogger
{
    void Log(string message);
}

public class ConsoleLogger : ILogger
{
    public void Log(string message) => Console.WriteLine($"[LOG] {message}");
}

public class UserService
{
    private readonly ILogger _logger; // "has-a" logger

    public UserService(ILogger logger) => _logger = logger;

    public void CreateUser(string name) => _logger.Log($"Creating user: {name}");
}
```

### When Inheritance IS Appropriate

```csharp
// Correct — Dog IS AN Animal (genuine is-a relationship)
public class Animal { public virtual void Speak() {} }
public class Dog : Animal { public override void Speak() => Console.WriteLine("Woof!"); }
```

Use inheritance when:
- The relationship is genuinely "is-a" — not just "can do" or "needs to use."
- The child extends base behavior (does not restrict or remove it).
- The hierarchy is shallow (max 2–3 levels).

### Common Interview Q&A

**Q: Why do we prefer composition over inheritance?**
> Because inheritance creates tight coupling. If the base class changes, all derived classes may break. Composition is more flexible — you can swap components, test them independently, and combine behaviors from multiple sources without being limited by single inheritance.

**Q: Give a real example where inheritance was the wrong choice.**
> The classic example: `Stack` inheriting from `List`. A stack is NOT a list — it should only have `Push`, `Pop`, and `Peek`. By inheriting from `List`, the stack exposes `Insert`, `Remove`, and `AddRange`, which break the stack contract. The correct design is `Stack` containing a `List` internally (composition).

**Q: Can you use both in the same design?**
> Yes. You often use inheritance for the main type hierarchy (Animal → Dog) and composition to add behaviors (Dog has-a `ICollar`, has-a `ITracker`). Inheritance defines what something IS; composition defines what it HAS or CAN DO.

> **Summary:** Prefer composition over inheritance for code reuse and flexibility. Use inheritance only for genuine "is-a" relationships with shallow hierarchies. Composition allows runtime swapping, better testing, and avoids fragile base class problems.

---

## 5. Dependency Injection

### What Is Dependency Injection?

Dependency Injection (DI) is a technique where an object's dependencies (the objects it needs to do its work) are **provided from outside** rather than created inside the object.

**Without DI:**
```csharp
public class OrderService
{
    private SqlOrderRepository _repo = new SqlOrderRepository(); // created inside!
    // Now OrderService is permanently coupled to SqlOrderRepository
}
```

**With DI:**
```csharp
public class OrderService
{
    private readonly IOrderRepository _repo;
    public OrderService(IOrderRepository repo) { _repo = repo; } // provided from outside
}
```

### Why DI Matters

- **Loose coupling:** `OrderService` depends on `IOrderRepository`, not a concrete class.
- **Testability:** In tests, inject a fake repository — no real database needed.
- **Flexibility:** Swap `SqlOrderRepository` for `MongoOrderRepository` without changing `OrderService`.
- **Implements DIP:** High-level classes depend on abstractions, not low-level details.

---

### 1. Constructor Injection (Most Common — Use This by Default)

Dependencies are passed through the constructor. The object is always in a valid, ready-to-use state.

```csharp
public interface IEmailService
{
    void Send(string to, string subject);
}

public class SmtpEmailService : IEmailService
{
    public void Send(string to, string subject)
        => Console.WriteLine($"SMTP: Sending '{subject}' to {to}");
}

public class UserRegistrationService
{
    private readonly IEmailService _emailService;

    // Constructor injection — dependency is required
    public UserRegistrationService(IEmailService emailService)
    {
        _emailService = emailService
            ?? throw new ArgumentNullException(nameof(emailService));
    }

    public void Register(string email)
    {
        // ... save user
        _emailService.Send(email, "Welcome!"); // uses the injected service
    }
}

// Usage
IEmailService emailSvc = new SmtpEmailService();
var regService = new UserRegistrationService(emailSvc);
regService.Register("ali@example.com");
```

**When to use:** Always use constructor injection for required dependencies.

---

### 2. Property Injection (For Optional Dependencies)

Dependencies are set through public properties after construction. The object can work without them.

```csharp
public class ReportService
{
    // Property injection — optional; has a default null-safe behavior
    public ILogger? Logger { get; set; }

    public void GenerateReport()
    {
        Logger?.Log("Generating report..."); // only logs if Logger was set
        Console.WriteLine("Report generated.");
    }
}

// Usage — Logger is optional
var service = new ReportService();
service.Logger = new ConsoleLogger(); // inject if needed
service.GenerateReport();
```

**When to use:** Only for optional, non-critical dependencies. Avoid for required dependencies — the object may be in an invalid state if the property is not set.

---

### 3. Method Injection (For Single-Use Dependencies)

The dependency is passed as a method parameter — used only for that specific call.

```csharp
public class NotificationService
{
    // Method injection — sender is provided per call, not stored
    public void Notify(string message, IMessageSender sender)
    {
        sender.Send(message);
    }
}

public interface IMessageSender { void Send(string message); }
public class SmsSender : IMessageSender { public void Send(string msg) => Console.WriteLine($"SMS: {msg}"); }
public class EmailSender : IMessageSender { public void Send(string msg) => Console.WriteLine($"Email: {msg}"); }

// Usage
var notifier = new NotificationService();
notifier.Notify("Your order is ready!", new SmsSender());
notifier.Notify("Invoice attached.", new EmailSender());
```

**When to use:** When the dependency varies per method call and should not be stored in the object.

---

### DI in ASP.NET Core

ASP.NET Core has a built-in DI container. You register services in `Program.cs`:

```csharp
// Program.cs
var builder = WebApplication.CreateBuilder(args);

// Register services with the DI container
builder.Services.AddScoped<IOrderRepository, SqlOrderRepository>();
builder.Services.AddScoped<IEmailService, SmtpEmailService>();
builder.Services.AddScoped<OrderService>(); // OrderService gets its deps injected automatically

var app = builder.Build();
```

**Service lifetimes:**

| Lifetime | Created | Use for |
|---|---|---|
| `AddSingleton` | Once per app | Config, shared caches |
| `AddScoped` | Once per HTTP request | DbContext, per-request services |
| `AddTransient` | Every time requested | Lightweight, stateless services |

In a controller, ASP.NET Core injects automatically:

```csharp
[ApiController]
[Route("api/orders")]
public class OrderController : ControllerBase
{
    private readonly OrderService _orderService;

    // ASP.NET Core injects OrderService automatically
    public OrderController(OrderService orderService)
    {
        _orderService = orderService;
    }

    [HttpPost]
    public IActionResult PlaceOrder([FromBody] string item)
    {
        _orderService.PlaceOrder(item);
        return Ok("Order placed.");
    }
}
```

### DI Injection Types Summary

| Type | How | Best for |
|---|---|---|
| **Constructor** | Via constructor parameter | Required dependencies (default choice) |
| **Property** | Via public property setter | Optional dependencies |
| **Method** | Via method parameter | Per-call varying dependencies |

> **Summary:** DI means "provide dependencies from outside instead of creating them inside." Constructor injection is the standard. ASP.NET Core's built-in container handles injection automatically. DI enables loose coupling, testability, and implements the Dependency Inversion Principle.

---

## 6. Interface-Based Design

### Program to Interfaces, Not Implementations

This is a fundamental OOD principle. Instead of depending on a specific class, depend on an interface (a contract).

**Without interface-based design:**
```csharp
// Coupled to a specific class — hard to change or test
public class OrderProcessor
{
    private SmtpEmailService _email = new SmtpEmailService(); // locked in
    public void Process() { _email.Send("done"); }
}
```

**With interface-based design:**
```csharp
// Depends on the contract — flexible and testable
public class OrderProcessor
{
    private readonly IEmailService _email;
    public OrderProcessor(IEmailService email) { _email = email; }
    public void Process() { _email.Send("done"); }
}
```

### Benefits of Interface-Based Design

| Benefit | Explanation |
|---|---|
| **Flexibility** | Swap implementations without changing the caller |
| **Testability** | Inject a mock/fake in unit tests |
| **Decoupling** | Caller does not know or care about the concrete type |
| **Extensibility** | Add new implementations without touching existing code |
| **Multiple implementations** | One interface, many concrete types (e.g., `ILogger` → Console, File, Cloud) |

### Practical C# Example

```csharp
// Define the contract
public interface IPaymentGateway
{
    bool Charge(string cardNumber, decimal amount);
}

// Implementation 1
public class StripeGateway : IPaymentGateway
{
    public bool Charge(string cardNumber, decimal amount)
    {
        Console.WriteLine($"Stripe: charging {amount:C} to {cardNumber}");
        return true;
    }
}

// Implementation 2
public class PayPalGateway : IPaymentGateway
{
    public bool Charge(string cardNumber, decimal amount)
    {
        Console.WriteLine($"PayPal: charging {amount:C} to {cardNumber}");
        return true;
    }
}

// Service depends only on the interface
public class CheckoutService
{
    private readonly IPaymentGateway _gateway;

    public CheckoutService(IPaymentGateway gateway)
    {
        _gateway = gateway;
    }

    public void Checkout(string card, decimal amount)
    {
        bool success = _gateway.Charge(card, amount);
        if (success) Console.WriteLine("Payment successful.");
    }
}

// Switch between gateways with zero change to CheckoutService
var checkout = new CheckoutService(new StripeGateway());
checkout.Checkout("4111-1111-1111-1111", 150m);

var checkout2 = new CheckoutService(new PayPalGateway());
checkout2.Checkout("4111-1111-1111-1111", 75m);
```

### Interface vs Abstract Class — Quick Reference

| | Interface | Abstract Class |
|---|---|---|
| **Purpose** | Define a contract/capability | Shared base with some implementation |
| **Multiple** | A class can implement many | A class can inherit only one |
| **State** | No fields | Can have fields |
| **Use when** | Defining what something can do | Defining what something is |

> **Summary:** Program to interfaces, not implementations. Interfaces define the "what"; concrete classes define the "how." This gives you flexibility, testability, and the ability to swap implementations without touching existing code.

---

## 7. Low-Level Design Principles

These principles guide everyday coding decisions. They are simpler than SOLID but equally important.

---

### High Cohesion

**Definition:** Everything inside a class should be closely related to its single purpose. A highly cohesive class does one thing well.

**Bad:** A `UserManager` class that handles login, profile editing, password reset, email notifications, and report generation.

**Good:** Separate classes: `LoginService`, `ProfileService`, `PasswordService`, `EmailService`.

**Interview answer:** "Cohesion measures how closely related a class's responsibilities are. High cohesion means one class, one clear purpose. Low cohesion means a class that does many unrelated things."

---

### Low Coupling

**Definition:** Classes should know as little as possible about each other. A change in one class should not force changes in others.

**Bad:**
```csharp
public class OrderService
{
    // Directly depends on the concrete class — high coupling
    private SqlDatabase _db = new SqlDatabase();
}
```

**Good:**
```csharp
public class OrderService
{
    // Depends on an abstraction — low coupling
    private readonly IDatabase _db;
    public OrderService(IDatabase db) { _db = db; }
}
```

**Rule of thumb:** High cohesion and low coupling go hand in hand. When each class has one clear responsibility, they naturally depend less on each other.

---

### Separation of Concerns (SoC)

**Definition:** Different concerns (UI, business logic, data access) should live in separate parts of the codebase and not mix.

**Example — MVC / Clean Architecture:**
- **Controller** — handles HTTP requests/responses (UI concern).
- **Service** — contains business logic (domain concern).
- **Repository** — handles database access (data concern).

```csharp
// Controller — only handles HTTP
[ApiController]
public class ProductController : ControllerBase
{
    private readonly ProductService _service;
    public ProductController(ProductService service) { _service = service; }

    [HttpGet("{id}")]
    public IActionResult Get(int id) => Ok(_service.GetProduct(id));
}

// Service — only contains business logic
public class ProductService
{
    private readonly IProductRepository _repo;
    public ProductService(IProductRepository repo) { _repo = repo; }
    public Product GetProduct(int id) => _repo.FindById(id);
}

// Repository — only talks to the database
public class SqlProductRepository : IProductRepository
{
    public Product FindById(int id) { /* SQL logic */ return new Product(); }
}
```

---

### DRY — Don't Repeat Yourself

**Definition:** Every piece of knowledge or logic should have one and only one representation in the codebase.

**Bad:**
```csharp
// Validation repeated in two places — if rules change, must update both!
public void RegisterUser(string email)
{
    if (string.IsNullOrEmpty(email)) throw new ArgumentException("Email required");
    // ...
}

public void UpdateEmail(string email)
{
    if (string.IsNullOrEmpty(email)) throw new ArgumentException("Email required");
    // ...
}
```

**Good:**
```csharp
private void ValidateEmail(string email)
{
    if (string.IsNullOrEmpty(email)) throw new ArgumentException("Email required");
}

public void RegisterUser(string email) { ValidateEmail(email); /* ... */ }
public void UpdateEmail(string email)  { ValidateEmail(email); /* ... */ }
```

**Important:** DRY is about duplicating logic, not necessarily code. Two methods that look similar but handle different business rules are NOT a DRY violation — do not blindly merge them.

---

### KISS — Keep It Simple, Stupid

**Definition:** Write the simplest code that works. Do not add complexity until it is necessary.

**Bad:**
```csharp
// Over-engineered for a simple greeting
public string GenerateGreeting(User user, IGreetingStrategy strategy,
    ILocalizationService localization, ITimeZoneProvider timezone)
{ /* ... */ }
```

**Good:**
```csharp
public string GetGreeting(string name) => $"Hello, {name}!";
```

**Interview answer:** "KISS means write simple, readable code. If a junior developer cannot understand it in 5 minutes, it is probably too complex. Complexity should be added only when the problem genuinely requires it."

---

### YAGNI — You Ain't Gonna Need It

**Definition:** Do not write code for features you think you might need in the future. Only write code for what is needed right now.

**Bad:**
```csharp
// Adding multilingual support, caching, and plugin system "just in case"
public class ProductService
{
    public Product GetProduct(int id, string language = "en",
        bool useCache = false, IPlugin? plugin = null) { /* ... */ }
}
```

**Good:**
```csharp
// Only implement what is actually required today
public class ProductService
{
    public Product GetProduct(int id) { /* ... */ return new Product(); }
}
```

**Interview answer:** "YAGNI prevents over-engineering. Only build what the current requirement asks for. Future requirements will be clearer when they actually arrive — and they often turn out to be different from what you imagined."

### Low-Level Principles Summary

| Principle | One-liner | Violation sign |
|---|---|---|
| **High Cohesion** | One class, one purpose | Class described with "and" |
| **Low Coupling** | Minimal dependencies between classes | Change in A breaks B unexpectedly |
| **SoC** | UI, logic, data in separate layers | SQL in a controller |
| **DRY** | One place for each piece of logic | Copy-pasted code blocks |
| **KISS** | Simplest code that works | Junior can't understand it |
| **YAGNI** | Only build what's needed now | Unused parameters and features |

> **Summary:** These six principles guide everyday coding. Together, they produce code that is simple, focused, non-repetitive, and just complex enough for the current problem. They complement SOLID and should become second nature.

---

## 8. Design Patterns for Fresh Graduates

Focus on these four patterns — they are the most commonly asked in fresh graduate interviews and are used in nearly every real project.

---

### Singleton

**Problem:** You need exactly one instance of a class throughout the application — creating multiple instances causes bugs or wastes resources (e.g., a configuration loader, a logging service).

**Use case:** App-wide configuration manager that reads settings once and is shared everywhere.

**C# implementation:**

```csharp
public sealed class AppConfig
{
    // The one and only instance
    private static AppConfig? _instance;
    private static readonly object _lock = new object();

    public string DatabaseUrl { get; private set; }

    // Private constructor — nobody can call new AppConfig()
    private AppConfig()
    {
        DatabaseUrl = "Server=prod-db;Database=AppDb;";
        Console.WriteLine("AppConfig initialized once.");
    }

    public static AppConfig Instance
    {
        get
        {
            if (_instance == null)
            {
                lock (_lock) // thread-safe
                {
                    if (_instance == null)
                        _instance = new AppConfig();
                }
            }
            return _instance;
        }
    }
}

// Usage
var config1 = AppConfig.Instance;
var config2 = AppConfig.Instance;
Console.WriteLine(ReferenceEquals(config1, config2)); // True — same object
Console.WriteLine(config1.DatabaseUrl);
```

**Modern C# alternative (simpler, thread-safe by default):**
```csharp
public sealed class AppConfig
{
    private static readonly Lazy<AppConfig> _instance =
        new Lazy<AppConfig>(() => new AppConfig());

    public static AppConfig Instance => _instance.Value;
    private AppConfig() { DatabaseUrl = "Server=prod-db;"; }
    public string DatabaseUrl { get; }
}
```

**Interview Q&A:**

**Q: How is Singleton different from a static class?**
> A Singleton is an object — it can implement interfaces, be passed as a parameter, and be injected. A static class cannot be instantiated and cannot implement interfaces. Singleton is preferred when you need OOP features.

**Q: What is the problem with Singleton?**
> It introduces global state, making unit testing harder (state from one test bleeds into another). In modern .NET, use `services.AddSingleton<T>()` with dependency injection instead of the classic pattern — it gives you the same single-instance behavior but with testability.

---

### Factory Method

**Problem:** You need to create objects, but the exact type to create depends on runtime conditions (user input, configuration, environment). Using `new ConcreteType()` directly couples the code to one specific type.

**Use case:** A notification system where the type of notification (email, SMS, push) is chosen at runtime based on user preference.

**C# implementation:**

```csharp
// Product interface
public interface INotification
{
    void Send(string message);
}

// Concrete products
public class EmailNotification : INotification
{
    public void Send(string message) => Console.WriteLine($"Email: {message}");
}

public class SmsNotification : INotification
{
    public void Send(string message) => Console.WriteLine($"SMS: {message}");
}

// Factory — creates the right type based on input
public static class NotificationFactory
{
    public static INotification Create(string type)
    {
        return type.ToLower() switch
        {
            "email" => new EmailNotification(),
            "sms"   => new SmsNotification(),
            _ => throw new ArgumentException($"Unknown notification type: {type}")
        };
    }
}

// Usage — client does not use new EmailNotification() or new SmsNotification()
INotification notification = NotificationFactory.Create("email");
notification.Send("Your order has shipped!");

notification = NotificationFactory.Create("sms");
notification.Send("OTP: 4521");
```

**Interview Q&A:**

**Q: Why use a factory instead of just using `new`?**
> Using `new` directly couples the caller to a specific class. The factory centralizes object creation — if the class name changes or you add a new type, you only change the factory, not every place that creates objects. It also supports OCP — add a new type by adding a `case` in the factory, not by modifying caller code.

**Q: What is the difference between a simple factory and the Factory Method pattern?**
> A simple factory (like above) is a static method that uses a `switch`. The Factory Method pattern uses inheritance — a base factory class has an abstract `Create()` method, and subclasses override it to create specific types. For fresh graduate interviews, either form is acceptable.

---

### Strategy

**Problem:** A class needs to perform a task, but the exact algorithm or behavior varies (sorting, discount calculation, payment method). Hard-coding all options with `if/switch` makes the class hard to extend.

**Use case:** A checkout system where the discount calculation depends on customer type — and new customer types will be added in the future.

**C# implementation:**

```csharp
// Strategy interface
public interface IPricingStrategy
{
    decimal CalculatePrice(decimal basePrice);
}

// Concrete strategies
public class RegularPricing : IPricingStrategy
{
    public decimal CalculatePrice(decimal basePrice) => basePrice;
}

public class StudentDiscount : IPricingStrategy
{
    public decimal CalculatePrice(decimal basePrice) => basePrice * 0.80m; // 20% off
}

public class SeniorDiscount : IPricingStrategy
{
    public decimal CalculatePrice(decimal basePrice) => basePrice * 0.70m; // 30% off
}

// Context — uses whatever strategy is injected
public class TicketBooking
{
    private IPricingStrategy _strategy;

    public TicketBooking(IPricingStrategy strategy)
    {
        _strategy = strategy;
    }

    public void SetStrategy(IPricingStrategy strategy) => _strategy = strategy;

    public decimal Book(decimal basePrice)
    {
        decimal finalPrice = _strategy.CalculatePrice(basePrice);
        Console.WriteLine($"Ticket price: {finalPrice:C}");
        return finalPrice;
    }
}

// Usage
var booking = new TicketBooking(new RegularPricing());
booking.Book(1000m); // Ticket price: Rs.1,000.00

booking.SetStrategy(new StudentDiscount());
booking.Book(1000m); // Ticket price: Rs.800.00

booking.SetStrategy(new SeniorDiscount());
booking.Book(1000m); // Ticket price: Rs.700.00
```

**Interview Q&A:**

**Q: What problem does Strategy solve?**
> It eliminates large `if/switch` blocks that select between algorithms. Instead of modifying the booking class every time a new pricing rule is added, you just add a new strategy class. This follows OCP.

**Q: Where is Strategy used in real ASP.NET Core applications?**
> Authentication schemes (JWT, Cookie, OAuth) are strategies. `IComparer<T>` for sorting is Strategy. Discount engines, shipping calculators, and export formats in enterprise apps all use Strategy.

---

### Observer

**Problem:** When one object's state changes, multiple other objects need to be notified and updated automatically — but the object should not know exactly who is listening.

**Use case:** When a new order is placed, the inventory service, email service, and analytics service all need to react — but the `Order` class should not directly call all of them.

**C# implementation:**

```csharp
// Observer interface
public interface IOrderObserver
{
    void OnOrderPlaced(string orderId, string item);
}

// Subject — the object being observed
public class OrderService
{
    private List<IOrderObserver> _observers = new();

    public void Subscribe(IOrderObserver observer)   => _observers.Add(observer);
    public void Unsubscribe(IOrderObserver observer) => _observers.Remove(observer);

    public void PlaceOrder(string orderId, string item)
    {
        Console.WriteLine($"Order #{orderId} placed for: {item}");
        // Notify all observers
        foreach (var observer in _observers)
            observer.OnOrderPlaced(orderId, item);
    }
}

// Concrete observers — react independently
public class InventoryObserver : IOrderObserver
{
    public void OnOrderPlaced(string orderId, string item)
        => Console.WriteLine($"  [Inventory] Reserving stock for '{item}'");
}

public class EmailObserver : IOrderObserver
{
    public void OnOrderPlaced(string orderId, string item)
        => Console.WriteLine($"  [Email] Sending confirmation for Order #{orderId}");
}

public class AnalyticsObserver : IOrderObserver
{
    public void OnOrderPlaced(string orderId, string item)
        => Console.WriteLine($"  [Analytics] Logging order event for '{item}'");
}

// Usage
var orderService = new OrderService();
orderService.Subscribe(new InventoryObserver());
orderService.Subscribe(new EmailObserver());
orderService.Subscribe(new AnalyticsObserver());

orderService.PlaceOrder("ORD-001", "Laptop");
```

**C# native Observer:** The `event` keyword is C#'s built-in Observer implementation:

```csharp
public class OrderService
{
    public event Action<string, string>? OrderPlaced;

    public void PlaceOrder(string orderId, string item)
    {
        Console.WriteLine($"Order #{orderId} placed.");
        OrderPlaced?.Invoke(orderId, item); // notify all subscribers
    }
}

// Subscribing with delegates
var svc = new OrderService();
svc.OrderPlaced += (id, item) => Console.WriteLine($"[Email] Confirming order {id}");
svc.OrderPlaced += (id, item) => Console.WriteLine($"[Inventory] Reserving {item}");
svc.PlaceOrder("ORD-002", "Phone");
```

**Interview Q&A:**

**Q: What is the difference between Observer and simple method calls?**
> With direct method calls, `OrderService` must know about `InventoryService`, `EmailService`, and `AnalyticsService` — tight coupling, all four classes are linked. With Observer, `OrderService` only knows about `IOrderObserver`. Observers register themselves — OrderService never needs to change when a new observer is added.

**Q: What is a memory leak risk with Observer?**
> If an observer subscribes (`+=`) but never unsubscribes (`-=`), it stays alive in memory as long as the subject exists — even after the observer is "done." Always unsubscribe in `Dispose()` or when the observer is no longer needed.

> **Summary — Design Patterns:** Singleton (one global instance), Factory Method (centralized object creation), Strategy (swappable algorithms), Observer (automatic notification of multiple interested parties). These four cover the most common fresh graduate interview questions on patterns.

---

## 9. Basic UML Diagrams

### Class Diagram Essentials

A class diagram shows classes, their attributes, methods, and the relationships between them. You will be asked to read or draw them in interviews.

**Class box structure:**

```
┌────────────────────┐
│     ClassName      │  ← Class name (bold, centered)
├────────────────────┤
│ - privateField     │  ← Attributes (fields/properties)
│ + publicField      │
├────────────────────┤
│ + PublicMethod()   │  ← Methods
│ - privateMethod()  │
└────────────────────┘
```

**Visibility symbols:**

| Symbol | Meaning | C# equivalent |
|---|---|---|
| `+` | Public | `public` |
| `-` | Private | `private` |
| `#` | Protected | `protected` |
| `~` | Package/Internal | `internal` |

---

### Relationship Notation Table

| Relationship | UML notation | Direction | Example |
|---|---|---|---|
| **Inheritance** | `──────▷` (solid line, open triangle) | Child → Parent | `Dog ──▷ Animal` |
| **Interface impl.** | `- - - -▷` (dashed line, open triangle) | Class → Interface | `Dog - -▷ IAnimal` |
| **Association** | `──────` (plain solid line) | Either way | `Teacher ── Student` |
| **Dependency** | `- - - ->` (dashed arrow) | User → Used | `OrderService - -> Email` |
| **Aggregation** | `──────◇` (solid, open diamond at whole) | Whole ◇── Part | `Dept ◇── Employee` |
| **Composition** | `──────◆` (solid, filled diamond at whole) | Whole ◆── Part | `House ◆── Room` |

---

### How to Read Interview UML Questions

When shown a UML diagram in an interview, follow this approach:

1. **Identify classes** — each box is a class. Note the name and key fields/methods.
2. **Read relationships** — trace the lines between classes. Use the table above to identify the type.
3. **Determine direction** — arrowheads and diamonds tell you which class owns or uses which.
4. **Spot design patterns** — if you see one class with a reference to an interface, and multiple classes implementing that interface, it could be Strategy or Observer.

**Example — reading a simple diagram:**

```
IPaymentGateway
     ▲
     │ (implements)
     │
StripeGateway    PayPalGateway

CheckoutService -----> IPaymentGateway
                (depends on)
```

Reading: `CheckoutService` depends on `IPaymentGateway` (interface). `StripeGateway` and `PayPalGateway` both implement `IPaymentGateway`. This is the Strategy pattern — `CheckoutService` can use either gateway interchangeably.

---

### UML Tips for Interviews

- You do not need to draw perfect UML — clear boxes and labeled lines are enough.
- Always label your lines with the relationship type if it is not obvious.
- Start with the main class/component in the center, radiate outward.
- Use `interface` or `<<interface>>` notation for interfaces.
- Do not over-complicate — interviewers want to see clear thinking, not artistic diagrams.

> **Summary:** Class diagrams show classes, their members, and relationships. Know the six relationship notations. In interviews, sketch clearly and label relationships. Recognize patterns from diagram structure.

---

## 10. OOD in Real Projects

The following are simplified but realistic design exercises at the fresh graduate level.

---

### Design 1: User Authentication Module

**Requirements:** Users can register, log in, and reset passwords. Passwords must be hashed. Login attempts should be logged.

**Classes and responsibilities:**

| Class/Interface | Responsibility |
|---|---|
| `IUserRepository` | Define contract for user data access |
| `SqlUserRepository` | Fetch and save users from/to database |
| `IPasswordHasher` | Define contract for hashing |
| `BcryptPasswordHasher` | Hash and verify passwords using bcrypt |
| `IAuthLogger` | Define contract for logging auth events |
| `ConsoleAuthLogger` | Log auth events to console |
| `AuthService` | Orchestrate registration, login, password reset |

**C# sketch:**

```csharp
public interface IUserRepository
{
    User? FindByEmail(string email);
    void Save(User user);
}

public interface IPasswordHasher
{
    string Hash(string password);
    bool Verify(string password, string hash);
}

public interface IAuthLogger
{
    void LogLogin(string email, bool success);
}

public class User
{
    public string Email        { get; set; } = "";
    public string PasswordHash { get; set; } = "";
}

public class AuthService
{
    private readonly IUserRepository _users;
    private readonly IPasswordHasher _hasher;
    private readonly IAuthLogger     _logger;

    public AuthService(IUserRepository users, IPasswordHasher hasher, IAuthLogger logger)
    {
        _users  = users;
        _hasher = hasher;
        _logger = logger;
    }

    public void Register(string email, string password)
    {
        if (_users.FindByEmail(email) != null)
            throw new InvalidOperationException("Email already registered.");

        var user = new User { Email = email, PasswordHash = _hasher.Hash(password) };
        _users.Save(user);
        Console.WriteLine($"User {email} registered.");
    }

    public bool Login(string email, string password)
    {
        var user = _users.FindByEmail(email);
        bool success = user != null && _hasher.Verify(password, user.PasswordHash);
        _logger.LogLogin(email, success);
        return success;
    }
}
```

**OOD principles applied:**
- **SRP:** Each class has one job (hashing, logging, data access, orchestration).
- **DIP:** `AuthService` depends on interfaces, not concrete classes.
- **OCP:** Add a new hasher (e.g., Argon2) by adding a new class — `AuthService` unchanged.
- **ISP:** Three focused interfaces instead of one fat one.

---

### Design 2: Notification Service

**Requirements:** The system sends notifications through multiple channels (email, SMS). New channels may be added in the future. Each notification has a type (info, warning, alert).

**Classes and responsibilities:**

| Class/Interface | Responsibility |
|---|---|
| `INotificationChannel` | Contract for sending a message |
| `EmailChannel` | Send via SMTP |
| `SmsChannel` | Send via SMS gateway |
| `NotificationMessage` | Hold notification content (message, type) |
| `NotificationService` | Route messages to the correct channels |

**C# sketch:**

```csharp
public enum NotificationType { Info, Warning, Alert }

public class NotificationMessage
{
    public string           Recipient { get; set; } = "";
    public string           Content   { get; set; } = "";
    public NotificationType Type      { get; set; }
}

public interface INotificationChannel
{
    void Send(NotificationMessage message);
}

public class EmailChannel : INotificationChannel
{
    public void Send(NotificationMessage msg)
        => Console.WriteLine($"[EMAIL] To:{msg.Recipient} | [{msg.Type}] {msg.Content}");
}

public class SmsChannel : INotificationChannel
{
    public void Send(NotificationMessage msg)
        => Console.WriteLine($"[SMS]   To:{msg.Recipient} | [{msg.Type}] {msg.Content}");
}

public class NotificationService
{
    private readonly List<INotificationChannel> _channels;

    public NotificationService(List<INotificationChannel> channels)
    {
        _channels = channels;
    }

    public void Notify(NotificationMessage message)
    {
        foreach (var channel in _channels)
            channel.Send(message); // Observer-like: all channels receive the message
    }
}

// Usage
var channels = new List<INotificationChannel>
{
    new EmailChannel(),
    new SmsChannel()
};
var service = new NotificationService(channels);
service.Notify(new NotificationMessage
{
    Recipient = "ali@example.com",
    Content   = "Your order has shipped.",
    Type      = NotificationType.Info
});
```

**OOD principles applied:**
- **OCP:** Add `PushChannel` by adding one class — `NotificationService` unchanged.
- **DIP:** Depends on `INotificationChannel`, not `EmailChannel`.
- **Composition:** `NotificationService` holds a list of channels.
- **Observer-like:** All channels are notified for every message.

---

### Design 3: Payment Processing Component

**Requirements:** Support multiple payment methods (credit card, PayPal). Payment results must be logged. The system must validate payment amounts before processing.

**Classes and responsibilities:**

| Class/Interface | Responsibility |
|---|---|
| `IPaymentGateway` | Contract for charging a payment |
| `CreditCardGateway` | Process credit card payments |
| `PayPalGateway` | Process PayPal payments |
| `IPaymentLogger` | Contract for logging payment events |
| `PaymentValidator` | Validate payment amount before processing |
| `PaymentProcessor` | Orchestrate validation, processing, logging |

**C# sketch:**

```csharp
public interface IPaymentGateway
{
    bool Charge(string account, decimal amount);
}

public class CreditCardGateway : IPaymentGateway
{
    public bool Charge(string card, decimal amount)
    {
        Console.WriteLine($"[CreditCard] Charged {amount:C} to {card}");
        return true;
    }
}

public class PayPalGateway : IPaymentGateway
{
    public bool Charge(string email, decimal amount)
    {
        Console.WriteLine($"[PayPal] Charged {amount:C} to {email}");
        return true;
    }
}

public interface IPaymentLogger
{
    void Log(string account, decimal amount, bool success);
}

public class ConsolePaymentLogger : IPaymentLogger
{
    public void Log(string account, decimal amount, bool success)
        => Console.WriteLine($"[LOG] Account:{account} Amount:{amount:C} Success:{success}");
}

public class PaymentValidator
{
    public void Validate(decimal amount)
    {
        if (amount <= 0)
            throw new ArgumentException("Payment amount must be positive.");
        if (amount > 1_000_000)
            throw new ArgumentException("Amount exceeds maximum limit.");
    }
}

public class PaymentProcessor
{
    private readonly IPaymentGateway _gateway;
    private readonly IPaymentLogger  _logger;
    private readonly PaymentValidator _validator;

    public PaymentProcessor(IPaymentGateway gateway, IPaymentLogger logger, PaymentValidator validator)
    {
        _gateway   = gateway;
        _logger    = logger;
        _validator = validator;
    }

    public bool Process(string account, decimal amount)
    {
        _validator.Validate(amount);
        bool result = _gateway.Charge(account, amount);
        _logger.Log(account, amount, result);
        return result;
    }
}

// Usage — swap PayPal for CreditCard with no change to PaymentProcessor
var processor = new PaymentProcessor(
    new CreditCardGateway(),
    new ConsolePaymentLogger(),
    new PaymentValidator()
);
processor.Process("4111-1111-1111-1111", 250m);
```

**OOD principles applied:**
- **SRP:** Validation, payment, and logging are each in separate classes.
- **DIP:** `PaymentProcessor` depends on `IPaymentGateway` and `IPaymentLogger`.
- **OCP:** Add `BankTransferGateway` without changing `PaymentProcessor`.
- **Strategy:** `IPaymentGateway` is a Strategy — swap gateways at runtime.

> **Summary:** Designing real modules means identifying classes (nouns), assigning responsibilities, defining interfaces for flexibility, and applying SOLID. Always start by listing what the system does → what classes those map to → how they connect.

---

## 11. Common Interview Questions

The following are the most frequently asked OOD questions for fresh graduates in Pakistani software companies (Systems Ltd, NetSol, 10Pearls, Arbisoft, Netsol, etc.) as well as product-based companies.

---

**Q: What is the difference between OOP and OOD?**
> OOP is a programming paradigm — it provides language features (classes, inheritance, polymorphism). OOD is a design activity — it is the process of deciding which classes to create, what they are responsible for, and how they relate, before writing code.

---

**Q: Explain SOLID with a real example.**
> SRP: A `UserService` only handles user logic — not email, not DB. OCP: Add a new payment method by adding a class, not editing the checkout code. LSP: A `Penguin` should not inherit from `Bird` if `Bird` has `Fly()`. ISP: Split a fat `IWorker` into `IWorkable`, `IEatable` so robots only implement `Work()`. DIP: `OrderService` depends on `IRepository`, not `SqlRepository`.

---

**Q: What is the difference between Aggregation and Composition?**
> In Aggregation, the parts can exist independently of the whole. Example: a `Department` has `Employees`, but employees still exist if the department closes. In Composition, the parts cannot exist without the whole. Example: a `House` has `Rooms` — demolish the house, the rooms cease to exist.

---

**Q: Why should we prefer composition over inheritance?**
> Because inheritance creates tight coupling — a change to the base class breaks all derived classes. Composition is more flexible: you can swap components at runtime, combine multiple behaviors, and test components independently. Use inheritance only for genuine "is-a" relationships.

---

**Q: What is Dependency Injection and why is it useful?**
> DI means providing an object's dependencies from outside rather than creating them inside. Instead of `new SqlRepository()` inside a service, the repository is passed via the constructor. This reduces coupling, enables unit testing with mock dependencies, and follows the Dependency Inversion Principle.

---

**Q: What is the Single Responsibility Principle?**
> A class should have one, and only one, reason to change. Each class should represent one concept and handle one responsibility. If a class changes when the database changes AND when the email template changes, it has two responsibilities and violates SRP.

---

**Q: What is the difference between an interface and an abstract class?**
> An interface defines a pure contract — no implementation, no state. A class can implement multiple interfaces. An abstract class can have both abstract methods and concrete implementations, and can have fields. Use interface for "can-do" capabilities; use abstract class for a shared base with some common implementation.

---

**Q: What design patterns do you know? Explain one.**
> I know Singleton (one global instance), Factory Method (centralize object creation), Strategy (swap algorithms at runtime), and Observer (notify multiple objects of state changes). [Pick one and explain the problem it solves, then walk through the C# code.]

---

**Q: What is the difference between low coupling and high cohesion?**
> High cohesion means a class does one thing well — all its methods and fields are closely related. Low coupling means classes have minimal dependencies on each other — a change in one does not ripple into others. Both go together: when each class has one clear job, they naturally depend less on each other.

---

**Q: How do you design a class? What steps do you follow?**
> First, I identify what the class is responsible for (SRP). Then I define its public interface — what other classes need from it. I use interfaces to express dependencies (DIP). I check whether the class needs to extend or compose. Finally I verify it follows OCP — would adding a new feature require modifying this class? If yes, I look for an abstraction.

---

**Q: What is the Open/Closed Principle? Give an example.**
> A class should be open for extension but closed for modification. Example: instead of adding `if (type == "Stripe")` in a checkout class every time a new payment provider is added, define `IPaymentGateway` and create a new `StripeGateway` class. The checkout class never changes — it just uses `IPaymentGateway`.

---

## 12. Common Mistakes

These are the most common OOD mistakes fresh graduates make in interviews and on the job.

---

| Mistake | What goes wrong | How to avoid it |
|---|---|---|
| **God class** | One class does everything — thousands of lines | Apply SRP; split by responsibility |
| **Inheritance for code reuse** | `UserService : Logger` — wrong relationship | Use composition; inject what you need |
| **Public fields everywhere** | Any code can put object in invalid state | Use private fields + public properties with validation |
| **Ignoring interfaces** | All code depends on concrete classes | Always code to interfaces for external dependencies |
| **Deep inheritance chains** | `Animal → Mammal → Pet → Dog → GoldenRetriever` | Keep hierarchies max 2-3 levels; use composition |
| **Creating dependencies inside classes** | `private X = new X()` — untestable, tightly coupled | Inject via constructor |
| **Violating LSP** | Derived class throws `NotImplementedException` | Redesign hierarchy; use interfaces |
| **Fat interfaces** | One interface with 10 methods — classes leave half empty | Split by ISP; one interface per capability |
| **Copy-pasting logic** | Same validation in 5 places | Extract to a shared method or class (DRY) |
| **Over-engineering** | Adding patterns/abstractions before they're needed | YAGNI; start simple, refactor when the need arises |
| **Missing encapsulation** | All fields public, all state exposed | Hide internal state; expose only what callers need |
| **Confusing Association and Composition** | Using "owns" when you mean "uses" | Check lifecycle — does destroying A destroy B? |
| **Naming classes vaguely** | `DataManager`, `Helper`, `Utility` | Name by responsibility: `OrderValidator`, `UserRepository` |
| **Not separating concerns** | SQL queries inside a controller action | Controllers handle HTTP; services handle logic; repos handle data |

---

### Common Mistakes in Interviews Specifically

- **Jumping to code too fast.** Interviewers want to see your design thinking first. Talk through your classes and relationships before writing any code.
- **Not asking clarifying questions.** Always ask: "Should I handle multiple payment types? Can a user have multiple roles?" Clarification shows senior thinking.
- **Forgetting to apply principles.** Write a class and immediately check: Does it have one responsibility? Does it depend on abstractions?
- **Designing in isolation.** Think about how classes connect. Draw boxes and arrows first.
- **Using patterns unnecessarily.** Do not say "I'll use the Factory pattern" if a simple `new Order()` is enough. Justify every design decision.

> **Summary:** The most common mistake is a God class that does too much. The second most common is using inheritance where composition is correct. Always code to interfaces, inject dependencies, keep classes small and focused, and start simple — add complexity only when needed.

---

# Final Cheat Sheet

> Revise this the day before your interview — 15 minutes is enough.

---

## OOD vs OOP

| | OOP | OOD |
|---|---|---|
| **What** | Programming paradigm | Design process |
| **When** | During coding | Before coding |
| **Output** | Working code | Class diagrams, design decisions |

---

## SOLID — One Line Each

| Principle | Rule | Red flag |
|---|---|---|
| **SRP** | One class, one reason to change | "and" in class description |
| **OCP** | Extend with new code, don't modify old | `if (type == "new")` in existing class |
| **LSP** | Subtypes must not break base expectations | `NotImplementedException` in override |
| **ISP** | Small focused interfaces | Empty/throwing interface methods |
| **DIP** | Depend on abstractions, inject concretes | `new ConcreteClass()` inside a class |

---

## Class Relationships — Quick Reference

| Relationship | Keyword | Lifecycle | Symbol |
|---|---|---|---|
| Association | "uses/knows" | Independent | `A ——— B` |
| Dependency | "uses temporarily" | Independent | `A - -> B` |
| Aggregation | "has-a (weak)" | Parts independent | `A ◇——— B` |
| Composition | "has-a (strong)" | Parts die with whole | `A ◆——— B` |
| Inheritance | "is-a" | Child depends on parent | `A ——▷ B` |

---

## Composition vs Inheritance

| | Inheritance | Composition |
|---|---|---|
| Relationship | is-a | has-a |
| Coupling | Tight | Loose |
| Runtime swap | No | Yes |
| Use when | Genuine is-a | Code reuse, flexibility |

---

## Dependency Injection Types

| Type | How injected | Use for |
|---|---|---|
| Constructor | Via constructor | Required dependencies |
| Property | Via public setter | Optional dependencies |
| Method | Via method parameter | Per-call dependencies |

---

## Low-Level Principles

| Principle | One-liner |
|---|---|
| High Cohesion | One class, one purpose |
| Low Coupling | Minimal cross-class dependencies |
| SoC | UI / Logic / Data in separate layers |
| DRY | No duplicate logic |
| KISS | Simplest code that works |
| YAGNI | Only build what's needed now |

---

## Design Patterns — Quick Reference

| Pattern | Problem | Key mechanism |
|---|---|---|
| **Singleton** | Need exactly one instance | Private constructor + static Instance |
| **Factory Method** | Create type varies at runtime | Centralized `Create()` method |
| **Strategy** | Algorithm varies, avoid if/switch | Interface + multiple implementations |
| **Observer** | Notify multiple objects on state change | Subscribe/Unsubscribe + notify loop |

---

## UML Notation Quick Reference

| Symbol | Meaning |
|---|---|
| `+` | public |
| `-` | private |
| `#` | protected |
| `──▷` | Inheritance |
| `- -▷` | Interface implementation |
| `◇──` | Aggregation (open diamond) |
| `◆──` | Composition (filled diamond) |
| `- ->` | Dependency (dashed arrow) |

---

## Interview Answer Template

When answering any OOD question, structure your answer as:

1. **Define it** — one clear sentence.
2. **State the problem it solves** — what goes wrong without it.
3. **Give a real-world analogy** — relatable, non-technical.
4. **Give a code example or class design** — minimal and clear.
5. **Mention when NOT to use it** — shows senior thinking.

---

## Top 5 Things Interviewers Check

1. Can you identify responsibilities and assign them to the right class?
2. Do you apply SOLID without being prompted?
3. Do you prefer interfaces and injection over concrete classes?
4. Can you explain why you chose composition or inheritance?
5. Do you design for change — not just for what works today?

---

*End of guide. Good luck in your interview.*
