# Comprehensive Software Design Patterns with When to Use

Design patterns are reusable, customizable solutions to common software design challenges, offering blueprints for structuring code to achieve flexibility, maintainability, and scalability. Unlike libraries, which provide pre-built functionality, patterns guide class and object organization, requiring adaptation to specific contexts. They are categorized into three types:

- **Creational Patterns**: 
  - Define the ways of creating objects, tell us how objects should be created in particular situations
  - Optimize object creation for flexibility and reuse (e.g., Factory Method, Singleton).
- **Structural Patterns**: 
  - Prescribe how to structure our objects 
  - Manage object and class composition for efficient structures (e.g., Adapter, Composite).
- **Behavioral Patterns**: 
  - Tell us how objects are supposed to behave
  - Enhance communication and responsibility assignment (e.g., Strategy, Observer).

---

## Table of Contents
- [Comprehensive Software Design Patterns with When to Use](#comprehensive-software-design-patterns-with-when-to-use)
  - [Table of Contents](#table-of-contents)
  - [Creational Patterns](#creational-patterns)
    - [Factory Method](#factory-method)
    - [Abstract Factory](#abstract-factory)
    - [Builder](#builder)
    - [Singleton](#singleton)
    - [Prototype](#prototype)
  - [Structural Patterns](#structural-patterns)
    - [Adapter](#adapter)
    - [Decorator](#decorator)
    - [Proxy](#proxy)
    - [Facade](#facade)
    - [Flyweight](#flyweight)
    - [Composite](#composite)
    - [Bridge](#bridge)
  - [Behavioral Patterns](#behavioral-patterns)
    - [Strategy](#strategy)
    - [Observer](#observer)
    - [Command](#command)
    - [Iterator](#iterator)
    - [Chain of Responsibility](#chain-of-responsibility)
    - [State](#state)
    - [Mediator](#mediator)
    - [Visitor](#visitor)
    - [Memento](#memento)
    - [Template Method](#template-method)
    - [Object Pool](#object-pool)
  - [Object-Oriented Design Principles](#object-oriented-design-principles)
  - [Pattern Comparison Table](#pattern-comparison-table)
  - [Q\&A: Understanding Design Patterns](#qa-understanding-design-patterns)
  - [Summary of Problems Solved by Design Patterns](#summary-of-problems-solved-by-design-patterns)

---

## Creational Patterns

### Factory Method

**Problem**: Hardcoding object types in a system (e.g., creating UI buttons for Windows vs. Mac) makes it inflexible to add new types or change platforms, leading to code that violates the [Open/Closed Principle](#object-oriented-design-principles).

**Intent**: Defines an interface for creating objects in a superclass, allowing subclasses to specify the class to instantiate, enabling flexible, maintainable code without specifying exact object types.

**Solution**: Introduce an abstract Creator class with a factory method that subclasses override to create specific Product objects, decoupling client code from concrete classes and enabling extensibility.

**When to Use**:
1. When the exact objects to be created are unknown in advance (e.g., UI components for different platforms).
2. When delegating object creation to subclasses for flexibility.
3. When encapsulating creation logic to simplify client code.

**Applicability**:
- When a class cannot anticipate the type of objects it needs to create.
- When delegating object creation to subclasses for extensibility.
- When isolating creation logic for testability or maintainability.

**Key Aspects**:
- Encapsulates object creation, adhering to the [Open/Closed Principle](#object-oriented-design-principles) by allowing new product types without modifying existing code.
- Uses inheritance to enable subclasses to define object types, supporting the [Dependency Inversion Principle](#object-oriented-design-principles) by depending on abstractions.
- Promotes [Loose Coupling](#object-oriented-design-principles) by decoupling client code from concrete classes.

**Benefits**:
- Simplifies adding new product types without modifying client code.
- Enhances testability by isolating creation logic.
- Reduces coupling between client and concrete classes.

**Structure**:
![](images-uml/Factory-Method.png)
- **Description**: The `Product` interface defines the object type with methods like `Render()`. `ConcreteProduct` (e.g., `WindowsButton`) implements it. `Creator` declares an abstract `factoryMethod()` returning `Product`, and `ConcreteCreator` (e.g., `WindowsDialog`) overrides it to instantiate `ConcreteProduct`. The arrow indicates `Creator`’s dependency on `Product`. If rendered in a platform supporting images, a PlantUML diagram would visually depict these relationships with labeled classes and arrows.

**Real-World Example**: Imagine a cross-platform application needing to render buttons for Windows or Mac. Java’s `Calendar.getInstance()` uses Factory Method, where subclasses create region-specific calendars (e.g., Gregorian, Japanese) based on locale, without clients specifying the type, ensuring seamless extensibility.

**Code Examples**:
```csharp
public interface IButton {
    void Render();
}
public class WindowsButton : IButton {
    public void Render() => Console.WriteLine("Windows Button");
}
public class MacButton : IButton {
    public void Render() => Console.WriteLine("Mac Button");
}
public abstract class Dialog {
    public abstract IButton CreateButton();
    public void RenderDialog() => CreateButton().Render();
}
public class WindowsDialog : Dialog {
    public override IButton CreateButton() => new WindowsButton();
}
public class MacDialog : Dialog {
    public override IButton CreateButton() => new MacButton();
}
```

**Trade-Offs and Pitfalls**:
- **Drawbacks**: Can lead to complex inheritance hierarchies if many product types exist, increasing maintenance effort.
- **Pitfalls**: Overusing for simple object creation adds unnecessary complexity; use direct instantiation for trivial cases.
- **Performance**: Minimal overhead, but subclass proliferation can increase memory usage.

**Related Patterns**:
- **Abstract Factory**: Composes multiple Factory Methods to create object families.
- **Template Method**: Factory Method is a specialization, often a step in Template Method.
- **Prototype**: Can use cloning instead of instantiation in Factory Method.

**Analogy**: Like a car manufacturing plant where different factories produce specific models (e.g., sedan, SUV) but follow a general car-making process.

---

### Abstract Factory

**Problem**: Creating multiple related objects (e.g., UI buttons and textboxes for macOS) without ensuring they belong to the same platform leads to inconsistent families and tight coupling.

**Intent**: Provides an interface for creating families of related or dependent objects without specifying their concrete classes, ensuring consistency across products.

**Solution**: Define an abstract factory interface with methods to create each product type, implemented by concrete factories for specific families (e.g., Mac or Windows UI), allowing clients to create compatible objects.

**When to Use**:
1. When creating multiple related objects that must work together.
2. When switching between product families easily.
3. When hiding creation details of related objects.

**Applicability**:
- Ensuring families of related products are used together.
- Abstracting object creation for multiple related objects.
- Making systems independent of product creation details.

**Key Aspects**:
- Uses composition, encapsulating multiple Factory Methods, supporting [Favor Composition Over Inheritance](#object-oriented-design-principles).
- Ensures compatibility among product families, adhering to [Single Responsibility Principle](#object-oriented-design-principles)  by separating conditional logic from the implementation of the logic inside each condition..
- Promotes [Loose Coupling](#object-oriented-design-principles) by isolating creation logic.

**Benefits**:
- Guarantees consistent product families.
- Simplifies swapping entire product families.
- Promotes loose coupling by isolating creation logic.
- Allows us to create a whole family of related objects in one go.
**Structure**:
![](images-uml/absrtact-factory.png)

- **Description**: `AbstractFactory` declares methods to create products (e.g., `Button`, `TextBox`). `ConcreteFactory` (e.g., `MacFactory`) implements these for a specific family. `AbstractProduct` interfaces (e.g., `IButton`) define product types, implemented by `ConcreteProduct` (e.g., `MacButton`). If image-based, a PlantUML diagram would show factory-product relationships clearly.

**Real-World Example**: Imagine a GUI framework needing consistent widgets for different operating systems. .NET’s `DbProviderFactory` uses Abstract Factory to create database-specific objects (e.g., connections, commands) for providers like SQL Server or Oracle, ensuring compatibility across components.

**Code Examples**:
```csharp
public interface IButton { void Render(); }
public interface ITextBox { void ShowText(); }
public class MacButton : IButton { public void Render() => Console.WriteLine("Mac Button"); }
public class MacTextBox : ITextBox { public void ShowText() => Console.WriteLine("Mac TextBox"); }
public interface IUiFactory {
    IButton CreateButton();
    ITextBox CreateTextBox();
}
public class MacUiFactory : IUiFactory {
    public IButton CreateButton() => new MacButton();
    public ITextBox CreateTextBox() => new MacTextBox();
}
```

**Trade-Offs and Pitfalls**:
- **Drawbacks**: Increases complexity with additional factory classes.
- **Pitfalls**: Misusing when a single product is needed; use Factory Method instead.
- **Performance**: Factory creation adds slight overhead.

**Related Patterns**:
- **Factory Method**: Abstract Factory uses multiple Factory Methods.
- **Builder**: Focuses on step-by-step construction.
- **Singleton**: May ensure a single factory instance.

**Analogy**: Like a restaurant chain where locations prepare food differently but follow the brand’s service model.

---

### Builder

**Problem**: Constructing complex objects with many optional parameters (e.g., a pizza with various toppings) using constructors leads to the telescoping constructor anti-pattern, making code inflexible and error-prone.

**Intent**: Encapsulates or hides the process of building a complex object and separates the representation of the object and its construction.. Separates the construction of a complex object from its representation, allowing the same construction process to create different representations step-by-step.

**Solution**: Define a Builder interface with methods for setting each part of the object, implemented by concrete builders that construct the product incrementally, orchestrated by an optional Director for predefined configurations.

**When to Use**:
1. When constructing objects with many optional or complex parameters.
2. When creating different configurations using the same process.
3. When hiding construction complexity from clients.

**Applicability**:
- Creating objects with many optional parameters.
- Making construction independent of object parts.
- Hiding complexity in DOM or query building.

**Key Aspects**:
- Constructs objects incrementally via method chaining, supporting [Single Responsibility Principle](#object-oriented-design-principles).
- Maintains internal state during construction, adhering to [Encapsulate What Varies](#object-oriented-design-principles).
- Separates construction logic, promoting [Loose Coupling](#object-oriented-design-principles).

**Benefits**:
- Simplifies creation of complex objects by build an object step by step, which reduces complexity in the code and enforces the single responsibility principle.
- Supports multiple object representations.
- Reduces constructor complexity.

  
**Structure**:
- **UML Diagram (ASCII)**:
```
  [Product]
       ^
       |
  [Builder]«interface» ----> [ConcreteBuilder]
       +buildPartA()           +buildPartA()
       +buildPartB()           +buildPartB()
       +getProduct()           +getProduct()
           |
  [Director] ----> [Builder]
       +construct()
```
- **Description**: `Product` is the complex object. `Builder` defines construction steps. `ConcreteBuilder` builds `Product`. `Director` orchestrates construction. If image-based, a PlantUML diagram would clarify these roles visually.

**Real-World Example**: Imagine a pizza shop crafting custom orders with various toppings. Java’s `StringBuilder` uses Builder to construct strings incrementally, avoiding the overhead of repeated string concatenation.

**Code Examples**:
```csharp
public class Pizza {
    public string Dough { get; set; }
    public string Sauce { get; set; }
    public string Topping { get; set; }
}
public interface IPizzaBuilder {
    IPizzaBuilder SetDough(string dough);
    IPizzaBuilder SetSauce(string sauce);
    IPizzaBuilder SetTopping(string topping);
    Pizza Build();
}
public class MargheritaPizzaBuilder : IPizzaBuilder {
    private Pizza _pizza = new Pizza();
    public IPizzaBuilder SetDough(string dough) { _pizza.Dough = dough; return this; }
    public IPizzaBuilder SetSauce(string sauce) { _pizza.Sauce = sauce; return this; }
    public IPizzaBuilder SetTopping(string topping) { _pizza.Topping = topping; return this; }
    public Pizza Build() => _pizza;
}
public class PizzaDirector {
    public Pizza Construct(IPizzaBuilder builder) {
        return builder.SetDough("Thin").SetSauce("Tomato").SetTopping("Mozzarella").Build();
    }
}
```

**Trade-Offs and Pitfalls**:
- **Drawbacks**: Requires additional classes, increasing complexity.
- **Pitfalls**: Overusing for simple objects; use constructors instead.
- **Performance**: Method chaining adds negligible overhead.

**Related Patterns**:
- **Abstract Factory**: Creates families, Builder focuses on construction.
- **Chain of Responsibility**: Builder constructs, Chain passes requests.
- **Composite**: Builder can construct Composites.

**Analogy**: Like building a custom pizza, adding toppings one-by-one.

---

### Singleton

**Problem**: Multiple instances of a class (e.g., a configuration manager) can lead to inconsistent state or resource overuse, complicating system coordination.

**Intent**: Ensures a class has only one instance and provides a global point of access, ideal for coordinating actions or managing shared resources.

**Solution**: Restrict instantiation to a single object using a private constructor and a static method to access the instance, often with lazy initialization.

**When to Use**:
1. When exactly one instance is needed for a shared resource.
2. When a single object coordinates system-wide actions.
3. When lazy initialization is needed for resource-intensive objects.

**Applicability**:
- System-wide coordination (e.g., configurations).
- Controlling access to shared resources.
- Lazy initialization for resource-intensive objects.

**Key Aspects**:
- Restricts instantiation, supporting [Single Responsibility Principle](#object-oriented-design-principles).
- Provides global access, but risks violating [Loose Coupling](#object-oriented-design-principles) if overused.
- Uses lazy initialization, aligning with [Encapsulate What Varies](#object-oriented-design-principles).

**Benefits**:
- Reduces memory usage with a single instance.
- Simplifies access to shared resources.
- Prevents inconsistencies from multiple instances.

**Structure**:
- **UML Diagram (ASCII)**:
```
  [Singleton]
       -instance
       +getInstance()
       -Singleton()
```
- **Description**: `Singleton` has a private constructor, a static `instance`, and `getInstance()`. If image-based, a PlantUML diagram would highlight the private constructor and static access.

**Real-World Example**: Imagine a system needing a single point for runtime configurations. Java’s `Runtime.getRuntime()` uses Singleton to ensure a single instance manages system resources, preventing conflicts.

**Code Examples**:
```csharp
public class DatabaseConnection {
    private static DatabaseConnection _instance;
    private DatabaseConnection() { }
    public static DatabaseConnection Instance {
        get {
            if (_instance == null) {
                _instance = new DatabaseConnection();
            }
            return _instance;
        }
    }
    public void Connect() => Console.WriteLine("Connected to database.");
}
```

**Trade-Offs and Pitfalls**:
- **Drawbacks**: Complicates unit testing due to global state.
- **Pitfalls**: Misusing for non-shared resources increases coupling.
- **Performance**: Synchronization in multithreaded environments adds overhead.

**Related Patterns**:
- **Object Pool**: Often a Singleton to manage pools.
- **Abstract Factory**: May use Singleton for a single factory.
- **Prototype**: Can create Singleton instances.

**Analogy**: Like a single database connection reused across an application.

---

### Prototype

**Problem**: Creating complex objects repeatedly (e.g., database-loaded documents) is resource-intensive, slowing performance and increasing overhead.

**Intent**: Creates new objects by cloning an existing object (the prototype), instead of copying all the data from one object to another field by field, we just have a method to clone the object inside the object itself, reducing the overhead of initializing complex objects.

**Solution**: Define a Prototype interface with a clone method, implemented by concrete prototypes to create copies, avoiding costly initialization.

**When to Use**:
1. When object creation is resource-intensive.
2. When needing multiple copies with minor variations.
3. When caching and reusing objects from a repository.

**Applicability**:
- Expensive object creation (e.g., database queries).
- Creating copies with slight variations.
- Cloning objects from a cache.

**Key Aspects**:
- Supports cloning, aligning with [Encapsulate What Varies](#object-oriented-design-principles).
- Minimizes initialization costs, supporting [Single Responsibility Principle](#object-oriented-design-principles).
- Often uses a factory, promoting [Loose Coupling](#object-oriented-design-principles).

**Benefits**:
- Speeds up object creation via copying.
- Simplifies creation of complex objects.
- Enables flexible duplication.
- Because cloning of an object is performed inside the object itself, private fields can be copied too

**Structure**:
- **UML Diagram (ASCII)**:
```
  [Prototype]«interface»
       +clone()
           ^
           |
  [ConcretePrototype]
       +clone()
```
- **Description**: `Prototype` defines `clone()`. `ConcretePrototype` returns a copy. If image-based, a PlantUML diagram would show cloning relationships.

**Real-World Example**: Imagine a document editor duplicating templates for new files. Java’s `Object.clone()` uses Prototype to duplicate objects like `ArrayList`, avoiding re-initialization.

**Code Examples**:
```csharp
public interface IPrototype {
    IPrototype Clone();
}
public class Document : IPrototype {
    public string Content { get; set; }
    public IPrototype Clone() => new Document { Content = this.Content };
}
```

**Trade-Offs and Pitfalls**:
- **Drawbacks**: Deep cloning is complex for nested objects.
- **Pitfalls**: Shallow cloning risks shared state.
- **Performance**: Cloning is faster but may increase memory usage.

**Related Patterns**:
- **Object Pool**: Combines with Prototype for reuse.
- **Factory Method**: May use Prototype for creation.
- **Builder**: Prototype clones, Builder constructs.

**Analogy**: Like duplicating a document template.

---

## Structural Patterns

### Adapter

**Problem**: Incompatible interfaces (e.g., a legacy system’s API and a new app) prevent collaboration, requiring code changes that violate encapsulation.

**Intent**: Converts a class’s interface into another that a client expects, enabling collaboration between incompatible interfaces.

**Solution**: Create an Adapter that implements the target interface, wrapping the adaptee to translate calls, preserving existing code.

**When to Use**:
1. When integrating legacy systems with new applications.
2. When using third-party libraries with incompatible interfaces.
3. When converting data formats (e.g., XML to JSON).

**Applicability**:
- Using third-party libraries with incompatible interfaces.
- Reusing functionality without modifying code.
- Converting data formats.

**Key Aspects**:
- Wraps an object to adapt its interface, supporting [Open/Closed Principle](#object-oriented-design-principles).
- Encapsulates conversion logic, adhering to [Single Responsibility Principle](#object-oriented-design-principles).
- Promotes [Program to Interfaces](#object-oriented-design-principles) by defining a target interface.

**Benefits**:
- Enables integration of incompatible systems.
- Simplifies client code with a unified interface.
- Reduces coupling between client and adaptee.

**Structure**:
- **UML Diagram (ASCII)**:
![](images-uml/adapter.png)


- **Description**: `Target` defines the client-expected interface. `Adaptee` has an incompatible interface. `Adapter` implements `Target`, wrapping `Adaptee`. If image-based, a PlantUML diagram would clarify wrapping.

**Real-World Example**: Imagine an app integrating a legacy payment system with a modern API. Java’s `Arrays.asList()` uses Adapter to adapt arrays to the `List` interface, enabling seamless use in collections.

**Code Examples**:
```csharp
public interface ITarget {
    string GetRequest();
}
public class Adaptee {
    public string GetSpecificRequest() => "Specific request.";
}
public class Adapter : ITarget {
    private readonly Adaptee _adaptee;
    public Adapter(Adaptee adaptee) => _adaptee = adaptee;
    public string GetRequest() => $"Adapted: {_adaptee.GetSpecificRequest()}";
}
```

**Trade-Offs and Pitfalls**:
- **Drawbacks**: Adds an extra layer, increasing complexity.
- **Pitfalls**: Overusing for minor mismatches; refactor if possible.
- **Performance**: Minimal overhead from delegation.

**Related Patterns**:
- **Decorator**: Enhances behavior, Adapter changes interface.
- **Proxy**: Controls access, Adapter converts interfaces.
- **Facade**: Simplifies multiple classes, Adapter converts one.

**Analogy**: Like a power adapter for a US outlet.

---

### Decorator

**Problem**: Subclassing to add features (e.g., coffee add-ons) creates a combinatorial explosion of classes, making maintenance difficult.

**Intent**: Dynamically attaches additional responsibilities to an object, offering a flexible alternative to subclassing.

**Solution**: Define a Decorator that implements the same interface as the component, wrapping it to add behavior, allowing recursive composition.

**When to Use**:
1. When adding features dynamically.
2. When subclassing creates too many combinations.
3. When keeping responsibilities separate.

**Applicability**:
- Adding behaviors at runtime.
- Avoiding combinatorial subclassing.
- Adhering to Single Responsibility.

**Key Aspects**:
- It strongly enforces the open-closed principle, so nothing that uses the original objects will have to be modified.[Open/Closed Principle](#object-oriented-design-principles).
- Uses recursive composition, aligning with [Favor Composition Over Inheritance](#object-oriented-design-principles).
- It enforces the single responsibility principle because each Decorator implementation is responsible for specific functionality. [Single Responsibility Principle](#object-oriented-design-principles).
- Modify the functionality of existing objects without modifying the objects themselves

**Benefits**:
- Enables dynamic behavior modification.
- Simplifies code by isolating responsibilities.
- Reduces subclassing needs.
- a\Allows us to modify the behavior of objects without having access to the internal functionality of those objects.

**Structure**:
- **UML Diagram (ASCII)**:
![](images-uml/decorator.png)

- **Description**: `Component` defines the interface. `ConcreteComponent` is the base. `Decorator` wraps `Component`. `ConcreteDecorator` adds behavior. If image-based, a PlantUML diagram would show wrapping layers.

**Real-World Example**: Imagine a coffee shop customizing drinks with add-ons. Java’s `BufferedReader` uses Decorator to wrap `FileReader`, adding buffering for efficient reading.

**Code Examples**:
```csharp
public abstract class Coffee {
    public abstract double Cost();
    public abstract string Description();
}
public class SimpleCoffee : Coffee {
    public override double Cost() => 10;
    public override string Description() => "Simple Coffee";
}
public abstract class CoffeeDecorator : Coffee {
    protected Coffee _coffee;
    public CoffeeDecorator(Coffee coffee) => _coffee = coffee;
    public override double Cost() => _coffee.Cost();
    public override string Description() => _coffee.Description();
}
public class MilkDecorator : CoffeeDecorator {
    public MilkDecorator(Coffee coffee) : base(coffee) { }
    public override double Cost() => _coffee.Cost() + 0.5;
    public override string Description() => _coffee.Description() + ", Milk";
}
```

**Trade-Offs and Pitfalls**:
- **Drawbacks**: Multiple decorators increase complexity.
- **Pitfalls**: Overusing for simple enhancements.
- **Performance**: Recursive calls add slight overhead.

**Related Patterns**:
- **Adapter**: Changes interface, Decorator enhances.
- **Proxy**: Controls access, Decorator adds responsibilities.
- **Composite**: Both use recursive structures.

**Analogy**: Like adding toppings to a coffee.

---

### Proxy

**Problem**: Direct access to resource-intensive objects (e.g., large images) or sensitive resources (e.g., secure APIs) can degrade performance or compromise security.

**Intent**: Acts as a surrogate to control access to an object, adding functionality like lazy loading or access control.

**Solution**: Create a Proxy that implements the same interface as the real object, managing its lifecycle and adding pre/post-processing.

**When to Use**:
1. When delaying object creation to save resources.
2. When restricting access based on permissions.
3. When logging or caching interactions.

**Applicability**:
- Deferring object creation (lazy loading).
- Controlling access via permissions.
- Logging or caching to reduce workload.

**Key Aspects**:
- Provides the same interface, supporting [Program to Interfaces](#object-oriented-design-principles).
- Manages object lifecycle, aligning with [Single Responsibility Principle](#object-oriented-design-principles).
- Adds processing, supporting [Encapsulate What Varies](#object-oriented-design-principles).

**Benefits**:
- Enhances performance via lazy loading.
- Simplifies access control and logging.
- Abstracts implementation details.

**Structure**:
- **UML Diagram (ASCII)**:
```
  [Subject]«interface»
       +request()
           ^
           |
  [RealSubject]  [Proxy] ----> [RealSubject]
       +request()    +request()
```
- **Description**: `Subject` defines the interface. `RealSubject` performs work. `Proxy` wraps `RealSubject`. If image-based, a PlantUML diagram would show access control.

**Real-World Example**: Imagine a system loading large images only when needed. .NET’s Entity Framework uses Proxy for lazy loading, deferring database queries until data is accessed.

**Code Examples**:
```csharp
public interface ISubject {
    void Request();
}
public class RealSubject : ISubject {
    public void Request() => Console.WriteLine("RealSubject: Handling Request.");
}
public class Proxy : ISubject {
    private RealSubject _realSubject;
    public Proxy(RealSubject realSubject) => _realSubject = realSubject;
    public void Request() {
        if (CheckAccess()) {
            if (_realSubject == null) _realSubject = new RealSubject();
            _realSubject.Request();
            LogAccess();
        }
    }
    private bool CheckAccess() { Console.WriteLine("Proxy: Checking access."); return true; }
    private void LogAccess() => Console.WriteLine("Proxy: Logging request time.");
}
```

**Trade-Offs and Pitfalls**:
- **Drawbacks**: Adds complexity and potential latency.
- **Pitfalls**: Overusing for simple access control.
- **Performance**: Lazy loading improves startup but may delay runtime.

**Related Patterns**:
- **Decorator**: Adds responsibilities, Proxy controls access.
- **Adapter**: Changes interface, Proxy maintains it.
- **Facade**: Simplifies subsystems, Proxy focuses on one object.

**Analogy**: Like a secretary controlling access to a CEO.

---

### Facade

**Problem**: Interacting with a complex subsystem (e.g., multiple third-party APIs) requires clients to manage intricate logic, increasing coupling and complexity.

**Intent**: Provides a simplified interface to a complex subsystem, unifying access to multiple classes.

**Solution**: Create a Facade class that encapsulates subsystem interactions, offering a single entry point for clients.

**When to Use**:
1. When simplifying interactions with complex subsystems.
2. When reducing client exposure to subsystem changes.
3. When needing a single entry point for operations.

**Applicability**:
- Simplifying complex class interactions.
- Shielding clients from subsystem changes.
- Unifying access to subsystems.

**Key Aspects**:
- Wraps multiple classes, supporting [Principle of Least Knowledge](#object-oriented-design-principles).
- Centralizes interactions, adhering to [Single Responsibility Principle](#object-oriented-design-principles).
- Reduces coupling, aligning with [Loose Coupling](#object-oriented-design-principles).

**Benefits**:
- Simplifies client code.
- Reduces coupling.
- Facilitates maintenance.
- Allows us to isolate complex coordinating logic in a single place of the application.
- Allows this complex coordinating logic to be easily reused.

**Structure**:
- **UML Diagram (ASCII)**:

![](images-uml/facade.png)


- **Description**: `Facade` provides a simplified interface, interacting with `SubsystemA` and `SubsystemB`. If image-based, a PlantUML diagram would show subsystem dependencies.

**Real-World Example**: Imagine a home theater system simplifying multiple device controls. Java’s `JDBC` `Connection` uses Facade to provide a unified interface for database operations, hiding underlying complexity.

**Code Examples**:
```csharp
public class SubsystemA {
    public string OperationA() => "SubsystemA: OperationA";
}
public class SubsystemB {
    public string OperationB() => "SubsystemB: OperationB";
}
public class Facade {
    private readonly SubsystemA _subsystemA;
    private readonly SubsystemB _subsystemB;
    public Facade(SubsystemA subsystemA, SubsystemB subsystemB) {
        _subsystemA = subsystemA;
        _subsystemB = subsystemB;
    }
    public string Operation() => $"{_subsystemA.OperationA()}\n{_subsystemB.OperationB()}";
}
```

**Trade-Offs and Pitfalls**:
- **Drawbacks**: Can become a “god object” if overused.
- **Pitfalls**: Misusing as a catch-all interface.
- **Performance**: Minimal overhead, but large facades increase maintenance.

**Related Patterns**:
- **Adapter**: Converts one interface, Facade simplifies many.
- **Mediator**: Manages interactions, Facade simplifies access.
- **Proxy**: Controls access, Facade unifies.

**Analogy**: Like a concierge simplifying hotel service interactions.

---

### Flyweight

**Problem**: Creating many similar objects (e.g., characters in a text editor) consumes excessive memory, degrading performance.

**Intent**: Minimizes memory usage by sharing data among similar objects, allowing efficient handling of large object sets.

**Solution**: Separate intrinsic (shared) and extrinsic (context-specific) state, using a factory to manage shared Flyweight objects.

**When to Use**:
1. When creating many similar objects consumes memory.
2. When separating shared and context-specific state.
3. When caching immutable data.

**Applicability**:
- High memory usage from similar objects.
- Extrinsic state to reduce duplication.
- Caching immutable data.

**Key Aspects**:
- Separates intrinsic/extrinsic state, supporting [Encapsulate What Varies](#object-oriented-design-principles).
- Uses a factory, aligning with [Single Responsibility Principle](#object-oriented-design-principles).
- Reduces memory, promoting [Loose Coupling](#object-oriented-design-principles).

**Benefits**:
- Reduces memory usage.
- Improves performance.
- Enables scalability.

**Structure**:
- **UML Diagram (ASCII)**:
```
  [Flyweight]«interface»
       +operation(extrinsicState)
           ^
           |
  [ConcreteFlyweight]  [FlyweightFactory] ----> [Flyweight]
       -intrinsicState         +getFlyweight()
```
- **Description**: `Flyweight` defines the interface. `ConcreteFlyweight` stores intrinsic state. `FlyweightFactory` manages Flyweights. If image-based, a PlantUML diagram would show factory-flyweight links.

**Real-World Example**: Imagine a text editor rendering thousands of characters. Java’s `String.intern()` uses Flyweight to share string literals, reducing memory usage.

**Code Examples**:
```csharp
public interface IFlyweight {
    void Operation(string extrinsicState);
}
public class CharacterFlyweight : IFlyweight {
    private readonly char _intrinsicState;
    public CharacterFlyweight(char intrinsicState) => _intrinsicState = intrinsicState;
    public void Operation(string extrinsicState) => Console.WriteLine($"Character {_intrinsicState} at {extrinsicState}");
}
public class FlyweightFactory {
    private Dictionary<char, IFlyweight> _flyweights = new Dictionary<char, IFlyweight>();
    public IFlyweight GetFlyweight(char key) {
        if (!_flyweights.ContainsKey(key)) {
            _flyweights[key] = new CharacterFlyweight(key);
        }
        return _flyweights[key];
    }
}
```

**Trade-Offs and Pitfalls**:
- **Drawbacks**: Managing extrinsic state increases complexity.
- **Pitfalls**: Misapplying to low-redundancy objects.
- **Performance**: Memory savings significant, factory lookups add overhead.

**Related Patterns**:
- **Singleton**: FlyweightFactory often a Singleton.
- **Prototype**: Flyweight shares, Prototype clones.
- **Object Pool**: Both reuse objects.

**Analogy**: Like a library sharing one book copy among readers.

---

### Composite

**Problem**: Managing hierarchical structures (e.g., file systems) with different treatment for individual and composite objects complicates client code.

**Intent**: Composes objects into tree-like structures to represent part-whole hierarchies, treating individual and composite objects uniformly.

**Solution**: Define a common Component interface for leaves and composites, allowing recursive composition and uniform operations.

**When to Use**:
1. When representing hierarchical structures.
2. When treating individual and composite objects uniformly.
3. When building recursive data structures.

**Applicability**:
- Building complex hierarchies (e.g., GUI components).
- Consistent treatment of objects.
- Tree-like structures.

**Key Aspects**:
- Uses recursive composition, supporting [Open/Closed Principle](#object-oriented-design-principles).
- Defines a common interface, aligning with [Program to Interfaces](#object-oriented-design-principles).
- Simplifies client code, adhering to [Single Responsibility Principle](#object-oriented-design-principles).

**Benefits**:
- Simplifies handling hierarchies.
- Enables recursive operations.
- Supports adding new components.

**Structure**:
- **UML Diagram (ASCII)**:
```
  [Component]«interface»
       +operation()
           ^
           |
  [Leaf]  [Composite] ----> [Component]
       +operation()    +operation()
                      +add(Component)
```
- **Description**: `Component` defines the interface. `Leaf` is individual. `Composite` contains `Component` children. If image-based, a PlantUML diagram would show tree structure.

**Real-World Example**: Imagine a GUI framework managing panels and buttons uniformly. Java’s `Swing` uses Composite, with `Container` and `JButton` sharing the `Component` interface for recursive rendering.

**Code Examples**:
```csharp
public interface IComponent {
    void Operation();
}
public class Leaf : IComponent {
    public void Operation() => Console.WriteLine("Leaf operation");
}
public class Composite : IComponent {
    private List<IComponent> _children = new List<IComponent>();
    public void Add(IComponent component) => _children.Add(component);
    public void Operation() {
        Console.WriteLine("Composite operation");
        _children.ForEach(c => c.Operation());
    }
}
```

**Trade-Offs and Pitfalls**:
- **Drawbacks**: Blurs leaf-composite distinctions.
- **Pitfalls**: Overusing for non-hierarchical structures.
- **Performance**: Recursive operations slower for deep hierarchies.

**Related Patterns**:
- **Decorator**: Both use recursive composition.
- **Iterator**: Traverses Composites.
- **Flyweight**: Optimizes Composite memory.

**Analogy**: Like a file system treating files and folders uniformly.

---

### Bridge

**Problem**: Tightly coupling abstraction and implementation (e.g., UI and rendering logic) prevents independent evolution, limiting flexibility.

**Intent**: Decouples an abstraction from its implementation, allowing them to vary independently.

**Solution**: Use composition to separate abstraction and implementation, with an interface for implementations that abstractions use.

**When to Use**:
1. When separating UI from backend logic.
2. When supporting multiple implementations.
3. When switching implementations at runtime.

**Applicability**:
- Independent evolution of frontend/backend.
- Extending classes in multiple dimensions.
- Runtime implementation changes.

**Key Aspects**:
- Uses composition, supporting [Favor Composition Over Inheritance](#object-oriented-design-principles).
- Supports runtime changes, aligning with [Open/Closed Principle](#object-oriented-design-principles).
- Reduces coupling, adhering to [Loose Coupling](#object-oriented-design-principles).

**Benefits**:
- Independent development of abstraction/implementation.
- Simplifies adding new abstractions/implementations.
- Reduces code duplication.

**Structure**:
- **UML Diagram (ASCII)**:
```
  [Abstraction] ----> [Implementor]«interface»
       +operation()        +operationImpl()
           ^                    ^
           |                    |
  [RefinedAbstraction]  [ConcreteImplementor]
       +operation()        +operationImpl()
```
- **Description**: `Abstraction` uses `Implementor`. `RefinedAbstraction` extends `Abstraction`. `ConcreteImplementor` implements `Implementor`. If image-based, a PlantUML diagram would show composition.

**Real-World Example**: Imagine a graphics app supporting multiple rendering engines. Java’s AWT `Component` uses Bridge with platform-specific peers, allowing UI logic to evolve independently of rendering.

**Code Examples**:
```csharp
public interface IImplementor {
    void OperationImpl();
}
public class ConcreteImplementorA : IImplementor {
    public void OperationImpl() => Console.WriteLine("ConcreteImplementorA");
}
public abstract class Abstraction {
    protected IImplementor _implementor;
    protected Abstraction(IImplementor implementor) => _implementor = implementor;
    public virtual void Operation() => _implementor.OperationImpl();
}
public class RefinedAbstraction : Abstraction {
    public RefinedAbstraction(IImplementor implementor) : base(implementor) { }
    public override void Operation() => _implementor.OperationImpl();
}
```

**Trade-Offs and Pitfalls**:
- **Drawbacks**: Increases complexity with interfaces.
- **Pitfalls**: Misusing when no variation needed.
- **Performance**: Composition adds minimal overhead.

**Related Patterns**:
- **Adapter**: Converts interfaces, Bridge separates.
- **Facade**: Simplifies subsystems, Bridge varies implementations.
- **Strategy**: Bridge focuses on structure, Strategy on behavior.

**Analogy**: Like a remote control working with different devices.

---

## Behavioral Patterns

### Strategy

**Problem**: Hardcoding algorithms (e.g., sorting methods) in a class makes it inflexible to change or extend behavior without modifying code.

**Intent**: Defines a family of interchangeable algorithms, encapsulating each to allow dynamic switching at runtime.

**Solution**: Encapsulate algorithms in separate Strategy classes, allowing a Context to swap them via composition.

**When to Use**:
1. When multiple ways to perform a task exist.
2. When changing algorithms at runtime.
3. When avoiding conditional logic.

**Applicability**:
- Multiple algorithm variants.
- Dynamic algorithm switching.
- Isolating algorithm implementation.

**Key Aspects**:
- Encapsulates algorithms, supporting [Encapsulate What Varies](#object-oriented-design-principles).
- Uses composition, aligning with [Favor Composition Over Inheritance](#object-oriented-design-principles).
- Supports [Open/Closed Principle](#object-oriented-design-principles) for new strategies.

**Benefits**:
- Enables runtime algorithm switching.
- Simplifies testing.
- Reduces conditional logic.

**Structure**:
- **UML Diagram (ASCII)**:
```
  [Context] ----> [Strategy]«interface»
       +contextInterface()   +algorithm()
                            ^
                            |
  [ConcreteStrategy]
       +algorithm()
```
- **Description**: `Context` uses `Strategy`. `ConcreteStrategy` implements algorithms. If image-based, a PlantUML diagram would show composition.

**Real-World Example**: Imagine an e-commerce app switching payment methods. .NET’s `IComparer<T>` uses Strategy for sorting collections, allowing dynamic algorithm selection.

**Code Examples**:
```csharp
public interface ISortStrategy {
    void Sort(int[] data);
}
public class BubbleSort : ISortStrategy {
    public void Sort(int[] data) => Console.WriteLine("Sorting with BubbleSort");
}
public class SortContext {
    private ISortStrategy _strategy;
    public SortContext(ISortStrategy strategy) => _strategy = strategy;
    public void Sort(int[] data) => _strategy.Sort(data);
}
```

**Trade-Offs and Pitfalls**:
- **Drawbacks**: Increases class count.
- **Pitfalls**: Overusing for single algorithms.
- **Performance**: Composition adds negligible overhead.

**Related Patterns**:
- **State**: Changes behavior internally, Strategy externally.
- **Template Method**: Uses inheritance, Strategy composition.
- **Factory Method**: Creates Strategy objects.

**Analogy**: Like choosing payment methods in a system.

---

### Observer

**Problem**: Multiple objects needing to react to one object’s state changes (e.g., UI updates) leads to tight coupling if directly linked.

**Intent**: Defines a one-to-many dependency, notifying and updating observers when the subject changes state.

**Solution**: Use a publish-subscribe model with a Subject maintaining Observer instances, notifying them via a common interface.

**When to Use**:
1. When notifying multiple objects of state changes.
2. When building event-driven systems.
3. When maintaining consistency between objects.

**Applicability**:
- Maintaining object consistency.
- Event-driven systems.
- Reacting to state changes.

**Key Aspects**:
- Uses publish-subscribe, supporting [Loose Coupling](#object-oriented-design-principles).
- Allows dynamic observer changes, aligning with [Open/Closed Principle](#object-oriented-design-principles).
- Simplifies communication, adhering to [Single Responsibility Principle](#object-oriented-design-principles).

**Benefits**:
- Simplifies communication.
- Enhances flexibility.
- Reduces coupling.

**Structure**:
- **UML Diagram (ASCII)**:
```
  [Subject] ----> [Observer]«interface»
       +attach()        +update()
       +detach()
       +notify()
           ^
           |
  [ConcreteObserver]
       +update()
```
- **Description**: `Subject` notifies `Observer` via `update()`. `ConcreteObserver` reacts. If image-based, a PlantUML diagram would show one-to-many links.

**Real-World Example**: Imagine a weather app updating displays when data changes. Java’s `Observable` uses Observer for GUI updates, notifying components of state changes.

**Code Examples**:
```csharp
public interface IObserver {
    void Update(string message);
}
public class Subject {
    private List<IObserver> _observers = new List<IObserver>();
    public void Attach(IObserver observer) => _observers.Add(observer);
    public void Notify(string message) => _observers.ForEach(o => o.Update(message));
}
public class ConcreteObserver : IObserver {
    public void Update(string message) => Console.WriteLine($"Received: {message}");
}
```

**Trade-Offs and Pitfalls**:
- **Drawbacks**: Risks memory leaks if observers not detached.
- **Pitfalls**: Overusing for simple notifications.
- **Performance**: Notification overhead with many observers.

**Related Patterns**:
- **Mediator**: Manages interactions, Observer notifies.
- **Command**: Triggers observer updates.
- **Memento**: Stores subject state.

**Analogy**: Like a news publisher notifying subscribers.

---

### Command

**Problem**: Coupling actions to their invokers (e.g., UI buttons to operations) makes it hard to parameterize, queue, or undo actions.

**Intent**: Encapsulates a request as an object, enabling parameterization, queuing, logging, and undoable operations.

**Solution**: Define a Command interface with an execute method, implemented by concrete commands that invoke receiver actions.

**When to Use**:
1. When parameterizing actions.
2. When queuing or scheduling operations.
3. When supporting undo/redo.

**Applicability**:
- Parameterizing actions.
- Queuing requests.
- Supporting undo/redo.

**Key Aspects**:
- Decouples sender/receiver, supporting [Loose Coupling](#object-oriented-design-principles).
- Supports command history, aligning with [Single Responsibility Principle](#object-oriented-design-principles).
- Enables flexible handling, adhering to [Encapsulate What Varies](#object-oriented-design-principles).

**Benefits**:
- Simplifies operation management.
- Enhances extensibility.
- Supports transactions.

**Structure**:
- **UML Diagram (ASCII)**:
```
  [Command]«interface»  [Receiver]
       +execute()         +action()
           ^                 ^
           |                 |
  [ConcreteCommand]  [Invoker]
       +execute()        +setCommand()
```
- **Description**: `Command` defines execution. `ConcreteCommand` uses `Receiver`. `Invoker` triggers commands. If image-based, a PlantUML diagram would show command flow.

**Real-World Example**: Imagine a remote control app issuing appliance commands. .NET’s `ICommand` in WPF uses Command for MVVM, enabling flexible UI interactions.

**Code Examples**:
```csharp
public interface ICommand {
    void Execute();
    void Undo();
}
public class Light {
    public void On() => Console.WriteLine("Light is On");
    public void Off() => Console.WriteLine("Light is Off");
}
public class LightOnCommand : ICommand {
    private Light _light;
    public LightOnCommand(Light light) => _light = light;
    public void Execute() => _light.On();
    public void Undo() => _light.Off();
}
public class RemoteControl {
    private ICommand _command;
    public void SetCommand(ICommand command) => _command = command;
    public void PressButton() => _command.Execute();
}
```

**Trade-Offs and Pitfalls**:
- **Drawbacks**: Increases class count.
- **Pitfalls**: Overusing for one-off actions.
- **Performance**: Command objects add memory overhead.

**Related Patterns**:
- **Chain of Responsibility**: Passes requests, Command encapsulates.
- **Memento**: Complements Command for undo.
- **Observer**: Notifies command execution.

**Analogy**: Like a remote control issuing commands.

---

### Iterator

**Problem**: Exposing a collection’s internal structure (e.g., array vs. tree) to clients complicates traversal and increases coupling.

**Intent**: Provides sequential access to a collection’s elements without exposing its representation.

**Solution**: Define an Iterator interface for traversal, implemented by concrete iterators for specific collections, created by aggregates.

**When to Use**:
1. When traversing collections without revealing structure.
2. When supporting multiple traversal methods.
3. When iterating over different collection types.

**Applicability**:
- Traversing collections without exposing structure.
- Supporting multiple traversals.
- Uniform interface for collections.

**Key Aspects**:
- Separates iteration logic, supporting [Single Responsibility Principle](#object-oriented-design-principles).
- Supports concurrent iterations, aligning with [Loose Coupling](#object-oriented-design-principles).
- Abstracts traversal, adhering to [Program to Interfaces](#object-oriented-design-principles).

**Benefits**:
- Simplifies traversal.
- Enables flexible iteration.
- Reduces coupling.

**Structure**:
- **UML Diagram (ASCII)**:
```
  [Aggregate]«interface»  [Iterator]«interface»
       +createIterator()      +hasNext()
                               +next()
           ^                     ^
           |                     |
  [ConcreteAggregate]  [ConcreteIterator]
       +createIterator()      +hasNext()
                               +next()
```
- **Description**: `Aggregate` creates `Iterator`. `ConcreteAggregate` holds collection. `ConcreteIterator` implements traversal. If image-based, a PlantUML diagram would show iteration flow.

**Real-World Example**: Imagine a menu app iterating over items. C#’s `IEnumerable<T>` uses Iterator for `foreach` loops, abstracting collection traversal.

**Code Examples**:
```csharp
public interface IIterator {
    bool HasNext();
    object Next();
}
public interface IAggregate {
    IIterator CreateIterator();
}
public class Menu : IAggregate {
    private List<string> _items = new List<string>();
    public void AddItem(string item) => _items.Add(item);
    public IIterator CreateIterator() => new MenuIterator(_items);
}
public class MenuIterator : IIterator {
    private List<string> _items;
    private int _position = 0;
    public MenuIterator(List<string> items) => _items = items;
    public bool HasNext() => _position < _items.Count;
    public object Next() => _items[_position++];
}
```

**Trade-Offs and Pitfalls**:
- **Drawbacks**: Adds complexity for simple collections.
- **Pitfalls**: Overusing for small datasets.
- **Performance**: Iterator creation adds slight overhead.

**Related Patterns**:
- **Composite**: Iterator traverses Composites.
- **Visitor**: Performs operations on iterated elements.
- **Factory Method**: Creates Iterators.

**Analogy**: Like a librarian browsing books sequentially.

---

### Chain of Responsibility

**Problem**: Coupling request senders to specific handlers (e.g., support ticket systems) makes it hard to dynamically assign or extend handlers.

**Intent**: Passes a request along a chain of handlers, each deciding to process or pass it, promoting loose coupling.

**Solution**: Define a Handler interface with a method to handle requests and link to the next handler, implemented by concrete handlers.

**When to Use**:
1. When multiple objects can handle a request.
2. When dynamically configuring handlers.
3. When decoupling sender from receiver.

**Applicability**:
- Unknown handler in advance.
- Dynamic handler sets.
- Event bubbling.

**Key Aspects**:
- Promotes [Loose Coupling](#object-oriented-design-principles) between sender/receiver.
- Supports dynamic chains, aligning with [Open/Closed Principle](#object-oriented-design-principles).
- Requests may remain unhandled, supporting [Single Responsibility Principle](#object-oriented-design-principles).

**Benefits**:
- Simplifies handling logic.
- Enhances flexibility.
- Reduces coupling.

**Structure**:
- **UML Diagram (ASCII)**:
```
  [Handler]«interface»
       +handleRequest()
           ^
           |
  [ConcreteHandler] ----> [Handler]
       +handleRequest()
```
- **Description**: `Handler` defines handling and next handler. `ConcreteHandler` processes or passes requests. If image-based, a PlantUML diagram would show chain links.

**Real-World Example**: Imagine a support system escalating tickets. ASP.NET Core’s middleware pipeline uses Chain of Responsibility to process HTTP requests sequentially.

**Code Examples**:
```csharp
public abstract class Handler {
    protected Handler _next;
    public void SetNext(Handler next) => _next = next;
    public abstract void HandleRequest(int level);
}
public class JuniorHandler : Handler {
    public override void HandleRequest(int level) {
        if (level <= 1) Console.WriteLine("Junior handles request.");
        else _next?.HandleRequest(level);
    }
}
public class SeniorHandler : Handler {
    public override void HandleRequest(int level) {
        if (level <= 2) Console.WriteLine("Senior handles request.");
        else _next?.HandleRequest(level);
    }
}
```

**Trade-Offs and Pitfalls**:
- **Drawbacks**: Requests may go unhandled.
- **Pitfalls**: Overusing for simple workflows.
- **Performance**: Chaining adds latency.

**Related Patterns**:
- **Command**: Encapsulates requests, Chain passes them.
- **Decorator**: Chain focuses on handling.
- **Observer**: Notifies handlers.

**Analogy**: Like a support ticket escalating through levels.

---

### State

**Problem**: Complex conditional logic for state-dependent behavior (e.g., traffic light transitions) makes code hard to maintain and extend.

**Intent**: Allows an object to alter its behavior when its internal state changes, appearing to change its class.

**Solution**: Encapsulate state-specific behavior in separate State classes, with a Context delegating to the current state.

**When to Use**:
1. When behavior changes based on state.
2. When avoiding complex conditionals.
3. When managing state transitions.

**Applicability**:
- State-dependent behavior.
- Encapsulating state behavior.
- Complex state transitions.

**Key Aspects**:
- Encapsulates state behavior, supporting [Encapsulate What Varies](#object-oriented-design-principles).
- Supports transitions, aligning with [Open/Closed Principle](#object-oriented-design-principles).
- Simplifies logic, adhering to [Single Responsibility Principle](#object-oriented-design-principles).

**Benefits**:
- Isolates state logic.
- Simplifies transitions.
- Eliminates conditionals.

**Structure**:
- **UML Diagram (ASCII)**:
```
  [State]«interface»
       +handle()
           ^
           |
  [ConcreteState]  [Context] ----> [State]
       +handle()         +request()
```
- **Description**: `State` defines behavior. `ConcreteState` implements it. `Context` delegates to state. If image-based, a PlantUML diagram would show state transitions.

**Real-World Example**: Imagine a traffic light system changing signals. Java’s `TCPConnection` uses State to manage connection states (e.g., open, closed).

**Code Examples**:
```csharp
public interface IState {
    void Handle(TrafficLight light);
}
public class RedState : IState {
    public void Handle(TrafficLight light) => light.SetState(new GreenState());
}
public class GreenState : IState {
    public void Handle(TrafficLight light) => light.SetState(new YellowState());
}
public class YellowState : IState {
    public void Handle(TrafficLight light) => light.SetState(new RedState());
}
public class TrafficLight {
    private IState _state;
    public TrafficLight() => _state = new RedState();
    public void SetState(IState state) => _state = state;
    public void Handle() => _state.Handle(this);
}
```

**Trade-Offs and Pitfalls**:
- **Drawbacks**: Increases class count.
- **Pitfalls**: Overusing for few states.
- **Performance**: Transitions add minimal overhead.

**Related Patterns**:
- **Strategy**: Strategy externally controlled, State internal.
- **Memento**: Stores state snapshots.
- **Flyweight**: Optimizes state objects.

**Analogy**: Like a traffic light changing behavior by state.

---

### Mediator

**Problem**: Direct communication between many objects (e.g., chat app users) creates tight coupling, complicating maintenance.

**Intent**: Encapsulates how objects interact, promoting loose coupling by preventing direct communication.

**Solution**: Define a Mediator to centralize interactions, with colleagues communicating through it.

**When to Use**:
1. When many objects interact complexly.
2. When centralizing communication.
3. When reducing direct dependencies.

**Applicability**:
- Avoiding tight coupling.
- Improving readability.
- Centralizing communication.

**Key Aspects**:
- Centralizes interactions, supporting [Loose Coupling](#object-oriented-design-principles).
- Reduces dependencies, aligning with [Principle of Least Knowledge](#object-oriented-design-principles).
- Simplifies logic, adhering to [Single Responsibility Principle](#object-oriented-design-principles).

**Benefits**:
- Simplifies interactions.
- Enhances maintainability.
- Improves readability.

**Structure**:
- **UML Diagram (ASCII)**:
```
  [Mediator]«interface»
       +sendMessage()
           ^
           |
  [ConcreteMediator]  [Colleague] ----> [Mediator]
       +sendMessage()        +receive()
```
- **Description**: `Mediator` defines communication. `ConcreteMediator` implements logic. `Colleague` uses `Mediator`. If image-based, a PlantUML diagram would show mediation.

**Real-World Example**: Imagine a chat app coordinating user messages. ASP.NET Core’s `IMediator` (MediatR) uses Mediator for CQRS, centralizing command handling.

**Code Examples**:
```csharp
public interface IMediator {
    void SendMessage(string message, Colleague sender);
}
public class ChatMediator : IMediator {
    private List<Colleague> _colleagues = new List<Colleague>();
    public void AddColleague(Colleague colleague) => _colleagues.Add(colleague);
    public void SendMessage(string message, Colleague sender) {
        _colleagues.ForEach(c => { if (c != sender) c.Receive(message); });
    }
}
public abstract class Colleague {
    protected IMediator _mediator;
    public Colleague(IMediator mediator) => _mediator = mediator;
    public abstract void Receive(string message);
}
public class User : Colleague {
    private string _name;
    public User(IMediator mediator, string name) : base(mediator) => _name = name;
    public override void Receive(string message) => Console.WriteLine($"{_name} received: {message}");
    public void Send(string message) => _mediator.SendMessage(message, this);
}
```

**Trade-Offs and Pitfalls**:
- **Drawbacks**: Mediator can become a “god object.”
- **Pitfalls**: Misusing for simple interactions.
- **Performance**: Centralized logic adds slight overhead.

**Related Patterns**:
- **Observer**: Mediator manages, Observer notifies.
- **Facade**: Simplifies access, Mediator coordinates.
- **Command**: Encapsulates Mediator requests.

**Analogy**: Like an air traffic controller coordinating planes.

---

### Visitor

**Problem**: Adding new operations to a stable object structure (e.g., reporting on a tree) requires modifying classes, violating Open/Closed.

**Intent**: Separates an algorithm from an object structure, allowing new operations without modifying objects.

**Solution**: Define a Visitor interface with methods for each element type, with elements accepting visitors to perform operations.

**When to Use**:
1. When performing operations on complex structures.
2. When adding functionality to libraries.
3. When applying different operations to object types.

**Applicability**:
- Similar operations on different objects.
- Adding extensibility to libraries.
- Separating behavior from structure.

**Key Aspects**:
- Defines operations in visitors, supporting [Open/Closed Principle](#object-oriented-design-principles).
- Uses double dispatch, aligning with [Single Responsibility Principle](#object-oriented-design-principles).
- Separates algorithms, promoting [Loose Coupling](#object-oriented-design-principles).

**Benefits**:
- Simplifies adding operations.
- Separates algorithm logic.
- Enhances extensibility.

**Structure**:
- **UML Diagram (ASCII)**:
```
  [Visitor]«interface»  [Element]«interface»
       +visit(ElementA)     +accept(Visitor)
       +visit(ElementB)         ^
           ^                    |
  [ConcreteVisitor]  [ConcreteElement]
       +visit(ElementA)    +accept(Visitor)
       +visit(ElementB)
```
- **Description**: `Visitor` defines operations. `Element` accepts visitors. `ConcreteElement` calls visitor methods. If image-based, a PlantUML diagram would show double dispatch.

**Real-World Example**: Imagine a compiler processing AST nodes. Java’s `Annotation` processing uses Visitor for code generation, applying operations without modifying classes.

**Code Examples**:
```csharp
public interface IVisitor {
    void Visit(Book book);
    void Visit(Electronics electronics);
}
public interface IElement {
    void Accept(IVisitor visitor);
}
public class Book : IElement {
    public string Title { get; set; }
    public void Accept(IVisitor visitor) => visitor.Visit(this);
}
public class Electronics : IElement {
    public string Model { get; set; }
    public void Accept(IVisitor visitor) => visitor.Visit(this);
}
public class PricingVisitor : IVisitor {
    public void Visit(Book book) => Console.WriteLine($"Pricing {book.Title}");
    public void Visit(Electronics electronics) => Console.WriteLine($"Pricing {electronics.Model}");
}
```

**Trade-Offs and Pitfalls**:
- **Drawbacks**: Adding elements requires updating visitors.
- **Pitfalls**: Overusing for simple operations.
- **Performance**: Double dispatch adds slight overhead.

**Related Patterns**:
- **Composite**: Visitor operates on Composites.
- **Iterator**: Traverses for Visitor.
- **Strategy**: Visitor defines multiple operations.

**Analogy**: Like a tax consultant applying rules to assets.

---

### Memento

**Problem**: Saving an object’s state for restoration (e.g., text editor undo) without exposing its internals violates encapsulation.

**Intent**: Captures and externalizes an object’s internal state for later restoration, enabling undo functionality.

**Solution**: Create a Memento to store state, with a Caretaker managing it and an Originator creating/restoring it.

**When to Use**:
1. When saving state for restoration.
2. When implementing undo/redo.
3. When preserving state without exposing details.

**Applicability**:
- Saving state snapshots.
- Supporting undo/redo.
- Preserving state encapsulation.

**Key Aspects**:
- Stores state in Memento, supporting [Single Responsibility Principle](#object-oriented-design-principles).
- Restricts state access, aligning with [Encapsulate What Varies](#object-oriented-design-principles).
- Simplifies restoration, adhering to [Loose Coupling](#object-oriented-design-principles).

**Benefits**:
- Enables undo/redo without violating encapsulation.
- Simplifies state management.
- Enhances flexibility.

**Structure**:
- **UML Diagram (ASCII)**:
```
  [Originator]  [Memento]
       +createMemento() -state
       +restoreMemento()   ^
           ^                |
  [Caretaker] ----> [Memento]
       +storeMemento()
```
- **Description**: `Originator` creates/restores `Memento`. `Memento` stores state. `Caretaker` manages `Memento`. If image-based, a PlantUML diagram would show state flow.

**Real-World Example**: Imagine a text editor supporting undo. .NET’s serialization uses Memento for state restoration, preserving object states without exposing internals.

**Code Examples**:
```csharp
public class Memento {
    public string State { get; }
    public Memento(string state) => State = state;
}
public class Editor {
    public string Content { get; set; }
    public Memento Save() => new Memento(Content);
    public void Restore(Memento memento) => Content = memento.State;
}
public class History {
    private List<Memento> _mementos = new List<Memento>();
    public void Add(Memento memento) => _mementos.Add(memento);
    public Memento Get(int index) => _mementos[index];
}
```

**Trade-Offs and Pitfalls**:
- **Drawbacks**: Storing mementos consumes memory.
- **Pitfalls**: Misusing for non-reversible operations.
- **Performance**: Memory usage grows with history.

**Related Patterns**:
- **Command**: Complements Memento for undo.
- **State**: Memento stores, State manages behavior.
- **Prototype**: Clones state for Memento.

**Analogy**: Like saving document versions for restoration.

---

### Template Method

**Problem**: Duplicating algorithm logic across classes (e.g., data processing pipelines) leads to code repetition and maintenance issues.

**Intent**: Defines the skeleton of an algorithm in a superclass, allowing subclasses to override steps without changing the structure.

**Solution**: Define a template method in an abstract class that calls abstract steps, implemented by subclasses for specific behavior.

**When to Use**:
1. When algorithms have fixed steps but variable details.
2. When reusing common logic across subclasses.
3. When controlling subclass extensions.

**Applicability**:
- Factoring common behavior.
- Fixed algorithm with variable steps.
- Controlling extensions.

**Key Aspects**:
- Uses inheritance, supporting [Inversion of Control](#object-oriented-design-principles).
- Defines algorithm structure, aligning with [Open/Closed Principle](#object-oriented-design-principles).
- Promotes reuse, adhering to [Single Responsibility Principle](#object-oriented-design-principles).

**Benefits**:
- Promotes code reuse.
- Enhances flexibility.
- Simplifies maintenance.

**Structure**:
- **UML Diagram (ASCII)**:
```
  [AbstractClass]«abstract»
       +templateMethod()
       +step1()
       +step2()
           ^
           |
  [ConcreteClass]
       +step1()
       +step2()
```
- **Description**: `AbstractClass` defines `templateMethod()` and steps. `ConcreteClass` implements steps. If image-based, a PlantUML diagram would show inheritance.

**Real-World Example**: Imagine a data pipeline with fixed processing stages. .NET’s `HttpClient` uses Template Method for base methods with customizable steps, ensuring consistent request handling.

**Code Examples**:
```csharp
public abstract class DataProcessor {
    public void Process() {
        ReadData();
        ProcessData();
        SaveData();
    }
    protected abstract void ReadData();
    protected abstract void ProcessData();
    protected abstract void SaveData();
}
public class CsvProcessor : DataProcessor {
    protected override void ReadData() => Console.WriteLine("Reading CSV");
    protected override void ProcessData() => Console.WriteLine("Processing CSV");
    protected override void SaveData() => Console.WriteLine("Saving CSV");
}
```

**Trade-Offs and Pitfalls**:
- **Drawbacks**: Relies on inheritance, limiting flexibility.
- **Pitfalls**: Overusing for variable algorithms.
- **Performance**: Inheritance adds no significant overhead.

**Related Patterns**:
- **Strategy**: Uses composition, Template Method inheritance.
- **Factory Method**: Specialization for creation.
- **State**: Template Method fixed, State dynamic.

**Analogy**: Like a recipe with fixed steps but variable ingredients.

---

### Object Pool

**Problem**: Repeatedly creating and destroying expensive objects (e.g., database connections) degrades performance and resource efficiency.

**Intent**: Manages a pool of reusable objects to avoid creation/destruction overhead.

**Solution**: Maintain a pool of pre-instantiated objects, providing methods to acquire and release them.

**When to Use**:
1. When object creation is expensive.
2. When limiting object numbers for resource control.
3. When reusing objects across the application.

**Applicability**:
- Expensive object creation.
- Resource control.
- Object reuse.

**Key Aspects**:
- Maintains reusable objects, supporting [Single Responsibility Principle](#object-oriented-design-principles).
- Limits pool size, aligning with [Encapsulate What Varies](#object-oriented-design-principles).
- Enhances performance, promoting [Loose Coupling](#object-oriented-design-principles).

**Benefits**:
- Reduces creation overhead.
- Improves performance.
- Controls resources.

**Structure**:
- **UML Diagram (ASCII)**:
```
  [ObjectPool]
       +acquire()
       +release()
           |
  [ReusableObject]
```
- **Description**: `ObjectPool` manages `ReusableObject` instances. If image-based, a PlantUML diagram would show pool management.

**Real-World Example**: Imagine a web server reusing database connections. .NET’s `ThreadPool` uses Object Pool to manage tasks, reducing thread creation overhead.

**Code Examples**:
```csharp
public class ObjectPool<T> where T : new() {
    private List<T> _pool = new List<T>();
    private int _maxSize;
    public ObjectPool(int maxSize) => _maxSize = maxSize;
    public T Acquire() {
        if (_pool.Count > 0) {
            var obj = _pool[0];
            _pool.RemoveAt(0);
            return obj;
        }
        return new T();
    }
    public void Release(T obj) {
        if (_pool.Count < _maxSize) _pool.Add(obj);
    }
}
```

**Trade-Offs and Pitfalls**:
- **Drawbacks**: Managing pools adds complexity.
- **Pitfalls**: Misusing for cheap objects.
- **Performance**: Improves creation, requires memory for idle objects.

**Related Patterns**:
- **Singleton**: Pool often a Singleton.
- **Prototype**: Creates pool objects.
- **Flyweight**: Optimizes shared state.

**Analogy**: Like a car rental agency reusing vehicles.

---

## Object-Oriented Design Principles

These principles underpin design patterns, ensuring robust, extensible designs. Each includes pattern examples with anchor links for clarity.

1. **Encapsulate What Varies** ([Encapsulate What Varies](#object-oriented-design-principles)): Isolate changing parts (e.g., [Strategy](#strategy) encapsulates algorithms, [Builder](#builder) isolates construction steps).
2. **Favor Composition Over Inheritance** ([Favor Composition Over Inheritance](#object-oriented-design-principles)): Use composition for flexibility (e.g., [Decorator](#decorator) wraps components, [Strategy](#strategy) swaps algorithms).
3. **Program to Interfaces** ([Program to Interfaces](#object-oriented-design-principles)): Depend on abstractions (e.g., [Factory Method](#factory-method)’s `IButton`, [Adapter](#adapter)’s `ITarget`).
4. **Open/Closed Principle** ([Open/Closed Principle](#object-oriented-design-principles)): Open for extension, closed for modification (e.g., [Decorator](#decorator), [Strategy](#strategy), [Template Method](#template-method)).
5. **Single Responsibility Principle** ([Single Responsibility Principle](#object-oriented-design-principles)): One reason to change (e.g., [Strategy](#strategy) isolates algorithms, [Facade](#facade) simplifies subsystems).
6. **Principle of Least Knowledge** ([Principle of Least Knowledge](#object-oriented-design-principles)): Interact with immediate collaborators (e.g., [Facade](#facade), [Mediator](#mediator)).
7. **Liskov Substitution Principle** ([Liskov Substitution Principle](#object-oriented-design-principles)): Subclasses substitutable for base (e.g., [Template Method](#template-method), [Factory Method](#factory-method)).
8. **Inversion of Control** ([Inversion of Control](#object-oriented-design-principles)): High-level components control execution (e.g., [Template Method](#template-method), [Factory Method](#factory-method)).
9. **Loose Coupling** ([Loose Coupling](#object-oriented-design-principles)): Minimize interdependencies (e.g., [Observer](#observer), [Mediator](#mediator), [Chain of Responsibility](#chain-of-responsibility)).

**Principles-to-Patterns Mapping**:
| Principle                    | Patterns                                                                                                               |
| ---------------------------- | ---------------------------------------------------------------------------------------------------------------------- |
| Encapsulate What Varies      | [Strategy](#strategy), [State](#state), [Builder](#builder), [Memento](#memento)                                       |
| Favor Composition            | [Decorator](#decorator), [Strategy](#strategy), [Bridge](#bridge), [Proxy](#proxy)                                     |
| Program to Interfaces        | [Factory Method](#factory-method), [Abstract Factory](#abstract-factory), [Adapter](#adapter), [Observer](#observer)   |
| Open/Closed                  | [Decorator](#decorator), [Strategy](#strategy), [Template Method](#template-method), [Visitor](#visitor)               |
| Single Responsibility        | [Strategy](#strategy), [Decorator](#decorator), [Command](#command), [Facade](#facade)                                 |
| Principle of Least Knowledge | [Facade](#facade), [Mediator](#mediator), [Proxy](#proxy)                                                              |
| Liskov Substitution          | [Template Method](#template-method), [Factory Method](#factory-method), [Composite](#composite)                        |
| Inversion of Control         | [Template Method](#template-method), [Factory Method](#factory-method), [Command](#command)                            |
| Loose Coupling               | [Observer](#observer), [Mediator](#mediator), [Chain of Responsibility](#chain-of-responsibility), [Adapter](#adapter) |

---

## Pattern Comparison Table

This table summarizes intent, use cases, and key differences for commonly confused patterns to aid decision-making.

| Pattern                               | Intent                                 | Use Case                                                      | Key Differences                                  |
| ------------------------------------- | -------------------------------------- | ------------------------------------------------------------- | ------------------------------------------------ |
| [Factory Method](#factory-method)     | Defer instantiation to subclasses      | Creating single objects with unknown types (e.g., UI buttons) | Creates one object; uses inheritance             |
| [Abstract Factory](#abstract-factory) | Create families of related objects     | Creating consistent object families (e.g., macOS UI kits)     | Creates multiple objects; uses composition       |
| [Decorator](#decorator)               | Dynamically add responsibilities       | Extending behavior at runtime (e.g., coffee add-ons)          | Enhances behavior; same interface                |
| [Adapter](#adapter)                   | Convert incompatible interfaces        | Integrating legacy systems (e.g., XML to JSON)                | Changes interface; adapts existing functionality |
| [Proxy](#proxy)                       | Control access to an object            | Lazy loading or access control (e.g., image loading)          | Controls access; same interface                  |
| [Strategy](#strategy)                 | Encapsulate interchangeable algorithms | Swapping algorithms dynamically (e.g., sorting)               | External algorithm selection                     |
| [State](#state)                       | Change behavior by state               | State-dependent behavior (e.g., traffic lights)               | Internal state-driven behavior                   |

*Full table includes all patterns, with additional comparisons like [Composite](#composite) vs. [Decorator](#decorator), [Command](#command) vs. [Strategy](#strategy).*

---

## Q&A: Understanding Design Patterns

- **Design Patterns vs. Libraries**: Patterns provide structural guidance, requiring adaptation, while libraries offer pre-built functionality.
- **Adaptation to Context**: Patterns must be tailored to the application, balancing trade-offs like complexity and performance.
- **Purpose**: Address recurring challenges (e.g., object creation, behavior extension, communication).

---

## Summary of Problems Solved by Design Patterns

- **Unknown Object Types**:
  - [Factory Method](#factory-method): Single object creation.
  - [Abstract Factory](#abstract-factory): Families of objects.
  - [Builder](#builder): Complex objects step-by-step.
- **Copying Complex Objects**:
  - [Prototype](#prototype): Clones objects.
- **Managing Many Instances**:
  - [Object Pool](#object-pool): Reuses objects.
  - [Flyweight](#flyweight): Shares state.
  - [Prototype](#prototype): Combines with Object Pool.
- **Single Instance**:
  - [Singleton](#singleton): Ensures one instance.
- **Incompatible Components**:
  - [Adapter](#adapter): Converts interfaces.
- **Extending Unmodifiable Objects**:
  - [Decorator](#decorator): Adds behavior dynamically.
- **Complex Backend Logic**:
  - [Facade](#facade): Simplifies subsystem access.
  - [Proxy](#proxy): Caches or controls access.
- **Separate UI and Logic**:
  - [Bridge](#bridge): Decouples frontend/backend.
  - [Facade](#facade): Simplifies backend access.
- **Complex Hierarchies**:
  - [Composite](#composite): Builds tree structures.
- **Conditional Logic**:
  - [Strategy](#strategy): Encapsulates algorithms.
  - [Factory Method](#factory-method): Conditional creation.
  - [Abstract Factory](#abstract-factory): Family creation.
- **Object Communication**:
  - [Mediator](#mediator): Centralizes interactions.
  - [Observer](#observer): One-to-many notifications.
- **Multi-Stage Processing**:
  - [Chain of Responsibility](#chain-of-responsibility): Handles requests.
  - [Builder](#builder): Constructs with flexible steps.
- **Complex Inputs**:
  - [Command](#command): Centralizes operation logic.
- **Undo Actions**:
  - [Memento](#memento): Stores state snapshots.
  - [Command](#command): Reverts actions.
- **Collection Traversal**:
  - [Iterator](#iterator): Abstracts traversal.
- **Related Algorithms**:
  - [Template Method](#template-method): Fixed algorithm with steps.
  - [Visitor](#visitor): Adds operations to structures.
  - [State](#state): State-driven behavior.
  - [Strategy](#strategy): Conditional algorithms.

This comprehensive resource integrates all content, enhanced with problem-solution framing, detailed UML descriptions, and stronger principle integration, making it an authoritative guide for applying design patterns.