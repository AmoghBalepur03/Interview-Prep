# Object-Oriented Programming (C++)

**Object-Oriented Programming (OOP)** models software as **objects** — entities with **state/properties (attributes)** and **behavior (methods)**. This note covers the full OOP interview surface in C++: classes and memory layout, constructors/destructors, the `const` keyword, copy semantics, `static`, friends, inheritance, polymorphism (with vtable/vptr internals), the diamond problem, abstraction, the SOLID principles, and the core design patterns.

The four pillars: **Encapsulation, Abstraction, Inheritance, Polymorphism.**

---

## 1. Objects, Classes, and Files

- An **object** has a specific state (attributes) and behavior (methods).
- The **size of an object** is typically the sum of the sizes of its **non-static** attributes. For an **empty class**, `sizeof(object) = 1` (the compiler ensures distinct objects have distinct addresses).

### Header Files and Include Guards
C++ disallows two classes with the same name in the same scope (compilation error). Importing a class and redefining it triggers this. Solutions:

```cpp
#ifndef ANIMAL_H
#define ANIMAL_H
class Animal { };
#endif
```

Or `#pragma once`.

**Best practices:** prefer including header (`.h`) files over `.cpp`; declare the class in a header (with guards), implement methods in a `.cpp`; don't redefine an imported class without renaming/scoping.

**Why headers in OOP:** encapsulation/abstraction (users see only the interface), reusability (include in many `.cpp`s), maintainability (change implementation without touching includers if the interface is stable), separation of concerns.

```cpp
// Person.h — declarations
#ifndef PERSON_H
#define PERSON_H
#include <string>
using namespace std;
class Person {
private:
    string name;
    int age;
public:
    Person(const string& name, int age);
    string getName() const;          // const → cannot modify members
    int getAge() const;
    void setName(const string& name);
    void setAge(int age);
    void display() const;
};
#endif
```

```cpp
// Person.cpp — implementations
#include "Person.h"
#include <iostream>
Person::Person(const string& name, int age) : name(name), age(age) {}
string Person::getName() const { return name; }
int Person::getAge() const { return age; }
void Person::setName(const string& name) { this->name = name; }
void Person::setAge(int age) { this->age = age; }
void Person::display() const { cout << "Name: " << name << ", Age: " << age << endl; }
```

Functions can be defined **inside** the class (implicitly inline) or **outside** using the scope resolution operator (`ClassName::SampleFunction(){}`), provided they're declared inside the class.

---

## 2. Memory Layout: Padding and Alignment

**Padding** — extra bytes inserted between members so each starts at a properly aligned address (CPUs access aligned data faster; misalignment can cause penalties or faults).

**Alignment** — the alignment requirement of a type usually equals its size: `char` → 1-byte, `int` → 4-byte, `double` → 8-byte boundaries.

```cpp
struct Example {
    char a;   // offset 0 (1 byte)
              // offset 1: padding (3 bytes)
    int  b;   // offset 4 (4 bytes)
};
// sizeof(Example) == 8
```

**Greedy alignment** — order members from **largest to smallest** to minimize padding.

---

## 3. Access Modifiers and Encapsulation

- **Public** — accessible anywhere.
- **Protected** — accessible within the class and derived classes.
- **Private** — accessible only within the class itself. **Default for a class is private.**

**Getters/setters** provide controlled access: validation/constraints before modification, read-only or write-only properties, encapsulation (hiding internals), and future-proofing (change implementation without breaking external code).

A **fully encapsulated class** has all data members private, accessed exclusively through public methods → control, data integrity (validation on access), flexibility (change internals freely), and easier maintenance.

---

## 4. Dynamic Memory Allocation

Allocating memory at **runtime** from the **heap** (free store), for when size/lifetime isn't known at compile time.

- **`new`** — allocates on the heap, returns a pointer. `int* p = new int;` or `int* arr = new int[10];`. On failure throws `std::bad_alloc` (or returns `nullptr` with `nothrow`).
- **`delete`** — deallocates. `delete p;` for a single object, `delete[] arr;` for arrays. Failing to `delete` causes **memory leaks**.
- C's `malloc`/`free` also work, but `new`/`delete` are preferred (they call constructors/destructors).
- Stack size is limited; the heap handles larger/longer-lived objects.

Essential for: variable-sized arrays, dynamic data structures (lists, trees, graphs), and large data sets.

---

## 5. Constructors

- **Default constructor** — no parameters; initializes the object (members get garbage unless initialized).
- **Parameterized constructor** — initializes members with provided values.
- **Member initializer list** — `Rectangle(int w, int h) : width(w), height(h) {}`.

**Important notes:** `this` points to the current object (`this == &currentObject`). Defining **any** constructor stops the compiler from auto-generating the default — define both explicitly if you need both.

### Copy Constructor
Creates a new object as a copy of an existing one: `ClassName(const ClassName& obj);`. If not defined, the compiler supplies one that does a **shallow (bitwise) copy**.

```cpp
Rectangle rect1(10, 20);
Rectangle rect2(rect1);   // copy constructor; behaves like rect2 = rect1;
```

### Member Initializer List — When It's Required
Members are initialized **in declaration order** (not initializer-list order). It is more efficient (direct initialization vs. default-construct-then-assign) and **required** for:
- **`const` members** (immutable after init):
  ```cpp
  class Test { const int t; public: Test(int val) : t(val) {} };
  ```
- **Reference members** (must bind at creation, can't be reseated):
  ```cpp
  class Test { int& ref; public: Test(int& r) : ref(r) {} };
  ```
- **Member objects without a default constructor:**
  ```cpp
  class A { public: A(int); };
  class B { A a; public: B(int x) : a(x) {} };
  ```
- **Base-class constructor calls:**
  ```cpp
  class Base { public: Base(int); };
  class Derived : public Base { public: Derived(int x) : Base(x) {} };
  ```

---

## 6. The `const` Keyword

Defines variables, pointers, or member functions whose values can't change after initialization — enforcing immutability and safety.

| Syntax | Meaning |
|---|---|
| `const int* ptr;` | Pointer to const int (**data** is const) |
| `int* const ptr;` | Const pointer to int (**pointer** can't change) |
| `const int* const ptr;` | Const pointer to const data |

A **const member function** (`void f() const;`) guarantees it won't modify member variables or call non-const members.

---

## 7. Shallow vs. Deep Copy

- **Shallow copy** — copies pointer **values** (shared memory). Changes via one object affect the other; risks dangling pointers and double-deletion. This is the default copy-constructor/assignment behavior.
- **Deep copy** — allocates new memory and recursively copies the pointed-to content; objects are fully independent. Required when a class manages dynamic resources.

```cpp
class MyClass {
    int* data;
public:
    MyClass(const MyClass& other) { data = new int(*other.data); }  // deep copy
    ~MyClass() { delete data; }
};
```

| Aspect | Shallow Copy | Deep Copy |
|---|---|---|
| Memory | Copies pointer values (shared) | Allocates new memory, copies content |
| Independence | Shared pointed-to data | Independent copies |
| Modifications | Affect both | Independent |
| Implementation | Default copy ctor | User-defined ctor + operator |
| Risks | Dangling pointers, double delete | More memory, slower |
| Performance | Faster | Slower |

### Copy Assignment Operator
`object2 = object1;` — by default does a member-wise (shallow) copy. Override it (along with the copy constructor and destructor — the **Rule of Three**) when managing resources.

---

## 8. Destructor

- Frees the object from memory / deallocates resources.
- Called automatically when the object goes out of scope (static allocation).
- For **dynamic** allocation you must call it via `delete object`.

---

## 9. The `static` Keyword

Belongs to the **class**, not any object.

- **Static member variable** — a single shared copy across all instances. Accessed via the class name (`ClassName::count`). Must be **defined and initialized outside** the class.
- **Static member function** — belongs to the class; can be called without an object; can only access static members (no `this`). Used for utility functions.
- **Static local variable** — inside a function, retains its value between calls; lifetime = whole program, visibility = the function; initialized once on first call.

```cpp
void counter() {
    static int count = 0;   // initialized once
    ++count;
    cout << count << endl;  // 1, 2, 3, ... across calls
}
```

---

## 10. Composition (has-a)

A class **contains objects of other classes** — a *has-a* relationship (a `Car` *has an* `Engine`). Composition implies **strong ownership**: contained objects' lifetimes are tied to the container; destroying the container destroys them. It supports encapsulation, modularity, and reuse (the `Engine` can be reused across vehicle types), and lets you swap implementations (electric engine) without changing the container.

### Composition vs. Inheritance
- **Inheritance (is-a)** — `Dog` *is an* `Animal`; for clear hierarchies and shared behavior.
- **Composition (has-a)** — `Car` *has an* `Engine`; for assembling classes with flexibility/modularity.
- Many patterns (Decorator, Adapter, Strategy) rely on composition to extend/change behavior at runtime. **"Favor composition over inheritance."**

---

## 11. Memory Assignment for Class Members

1. **Non-static data members** — stored per-object in a contiguous block (each object has its own copy).
2. **Static data members** — allocated once in static/global memory, shared across instances.
3. **Access specifiers** — control access only; **don't affect memory layout**.
4. **Non-static member functions** — **not** stored per object; code lives in a shared code segment; a hidden **`this` pointer** is passed when called.
5. **Static member functions** — also in the shared code segment; no `this`.
6. **Virtual functions** — the compiler creates a **vtable** (per class); each object stores a hidden **vptr** (adds pointer-size, e.g., 4/8 bytes) used for runtime dispatch.
7. **Dynamic allocation** — the object stores only the pointer; the actual memory lives on the heap.

---

## 12. Virtual Functions, vtable, and vptr

A **virtual function** is declared with `virtual` in the base class, enabling **runtime polymorphism (dynamic dispatch)** — the call resolves at runtime based on the **actual object type**, not the pointer/reference type.

- Normal member functions use **early (compile-time) binding**.
- Virtual functions need **late (runtime) binding**.

### The Mechanism
- **vtable (virtual table)** — a hidden, compiler-generated **static** table per class containing function pointers to the most-derived implementations. **One vtable per class.**
- **vptr (virtual pointer)** — a hidden pointer in **each object** pointing to its class's vtable (typically at the start of the object's layout). On a virtual call, the vptr finds the vtable, then the function pointer is fetched and called.

```cpp
class Base {
public:
    virtual void fun1() { std::cout << "Base fun1\n"; }
    virtual void fun2() { std::cout << "Base fun2\n"; }
};
class Derived : public Base {
public:
    void fun1() override { std::cout << "Derived fun1\n"; }
    // fun2() not overridden → uses Base's fun2
};
int main() {
    Base* bptr;
    Derived d;
    bptr = &d;
    bptr->fun1();   // Derived fun1  (vtable lookup)
    bptr->fun2();   // Base fun2     (vtable lookup)
}
```

**Key points:** one vtable per class (shared by all instances); one vptr per object; the vptr increases object size by a pointer; virtual calls add a small runtime overhead but enable polymorphism. Overridden entries point to the derived version; otherwise the base version. **Virtual functions cannot be static** (they're tied to instances).

---

## 13. Friend Functions and Classes

A **friend function** is declared inside a class with `friend`, granting it access to **private/protected** members although it isn't a member.

- Declared inside the class: `friend return_type name(args);`.
- **Not** a member → cannot be called via an object, has **no `this`**, must receive an object explicitly.
- Can be a **global function** or a **member of another class**.

```cpp
class MyClass {
    int secret;
public:
    MyClass() : secret(42) {}
    friend void friendFunction(MyClass& obj);   // friend declaration
};
void friendFunction(MyClass& obj) { std::cout << obj.secret << std::endl; }
```

The friend declaration appears only in the granting class; the definition is outside.

**Friend member of another class** (needs forward declaration):

```cpp
class B;                                   // forward declaration
class A {
    int data;
public:
    A() : data(10) {}
    friend void B::display(A&);            // B's member is a friend of A
};
class B { public: void display(A& obj); };
void B::display(A& obj) { std::cout << obj.data << std::endl; }   // after both known
```

**Friend class** — all member functions of the friend class can access private/protected members:

```cpp
class A {
    friend class B;     // B is a friend of A
    int secret;
public:
    A() : secret(100) {}
};
class B { public: void showSecret(A& a) { std::cout << a.secret << std::endl; } };
```

**Use cases:** operator overloading needing private access, utility functions tied to class internals, and interaction between closely related classes — providing **controlled** access without making data public.

| Aspect | Friend Function/Class | Member Function |
|---|---|---|
| Membership | Not a member | Member |
| Access to private data | Yes (if declared friend) | Yes (default) |
| `this` pointer | No | Yes |
| Invocation | Like a normal function | Via an object |
| Encapsulation | Slightly weakened | Maintained |

---

## 14. Inheritance

One class (**derived/child**) acquires the properties and behaviors of another (**base/parent**).

```cpp
class Derived : access_modifier Base { /* ... */ };
```

If the access modifier is omitted, **private inheritance** is the default.

- The derived class can access **public** and **protected** base members; **private** base members are inaccessible.
- **Construction order:** base constructor first, then derived. **Destruction order:** derived destructor first, then base (reverse).

### Access Control and Inheritance Types
| Inheritance | Public members become | Protected become | Private |
|---|---|---|---|
| **Public** | Public | Protected | Not inherited |
| **Protected** | Protected | Protected | Not inherited |
| **Private (default)** | Private | Private | Not inherited |

### Polymorphism and Access via Base Pointer
```cpp
BaseClass* ptr = new ChildClass();
```
`ptr` can access only members in `BaseClass`'s interface (public/protected). Members unique to `ChildClass` aren't accessible through `ptr` unless exposed via the base interface. **Virtual functions** let `ptr` call the correct derived implementation.

### Types of Inheritance
- **Single** — one base class.
- **Multiple** — more than one base class.
- **Hierarchical** — many derived classes from one base.
- **Multilevel** — a derived class becomes a base (Ancestor → Parent → Child).
- **Hybrid** — a mix.

### Inheritance Ambiguity
In multiple inheritance, if both base classes have a method with the same name, use the **scope resolution operator**:

```cpp
object.BaseClass1::func();
object.BaseClass2::func();
```

---

## 15. Polymorphism

### Compile-Time (Static) Polymorphism — early/static binding
**Function overloading** — same name, different parameter lists (number/types). The compiler selects based on arguments. **Cannot overload by return type alone.**

```cpp
void func(int a);
void func(double a);
void func(int a, int b);
```

**Operator overloading** — custom behavior for operators on user-defined types:

```cpp
class Complex {
    int real, imag;
public:
    Complex(int r = 0, int i = 0) : real(r), imag(i) {}
    Complex operator+(const Complex& obj) {
        return Complex(real + obj.real, imag + obj.imag);
    }
};
```

**Operators that cannot be overloaded:** scope resolution `::`, member access `.`, pointer-to-member `.*`, ternary `?:`, `sizeof`, `typeid`, `alignof`, `decltype`, and the casting operators.

### Runtime (Dynamic) Polymorphism — late binding
Achieved via **function overriding** with **virtual functions**; resolved at runtime. Cannot be achieved by data members (those bind to the static type). A base-class pointer/reference to a derived object calls the **base** function unless it's `virtual`.

**Function overriding** — a derived class redefines a base function with the **same signature**; with `virtual` on the base, a base pointer/reference to a derived object calls the derived version.

```cpp
class Employee {
public:
    virtual void raiseSalary() { std::cout << "Employee raise\n"; }
};
class Engineer : public Employee {
public:
    void raiseSalary() override { std::cout << "Engineer raise\n"; }
};
int main() {
    Employee* emp = new Engineer();
    emp->raiseSalary();    // "Engineer raise" — virtual dispatch
}
```

Without `virtual`, the call binds to the pointer type (`Employee*`) at compile time.

| Type | Binding | Mechanism | Example |
|---|---|---|---|
| Compile-time | Early | Function/operator overloading | `func(int)`, `func(double)` |
| Runtime | Late | Virtual functions, overriding | overridden `raiseSalary` |

---

## 16. The Diamond Problem and Virtual Inheritance

Occurs when a class inherits from two classes that both inherit from the same base → ambiguity and **multiple copies** of the base. **Virtual inheritance** ensures a single shared base instance.

```cpp
class Base { public: int value; };
class Derived1 : public virtual Base {};
class Derived2 : public virtual Base {};
class MostDerived : public Derived1, public Derived2 {};

MostDerived obj;
obj.value = 10;   // unambiguous — single Base instance
```

**Memory layout with a virtual base:** instead of each derived class holding its own copy, a single shared base instance exists, reached via an internal pointer (vbase pointer). All paths reference the same instance:

```cpp
MostDerived obj;
obj.value = 10;
Derived1* d1 = &obj; d1->value = 20;   // same Base::value
Derived2* d2 = &obj; d2->value = 30;   // same Base::value
```

**Key points:** single instance, avoids ambiguity, saves memory, adds some complexity/indirection.

---

## 17. Abstraction: Abstract Classes and Interfaces (added detail)

**Abstraction** exposes essential features while hiding implementation.

- **Pure virtual function** — `virtual void area() = 0;` — has no implementation in the base; **must** be overridden by concrete derived classes.
- **Abstract class** — has at least one pure virtual function; **cannot be instantiated**; may also have implemented methods and data.
- **Interface (in C++)** — an abstract class with **only** pure virtual functions (and a virtual destructor) — a pure contract.

```cpp
class Shape {                       // abstract class / interface
public:
    virtual double area() const = 0;          // pure virtual
    virtual void draw() const { /* default */ }
    virtual ~Shape() = default;               // virtual destructor (important!)
};
class Circle : public Shape {
    double r;
public:
    Circle(double r) : r(r) {}
    double area() const override { return 3.14159 * r * r; }
};
```

| | Abstract Class | Interface |
|---|---|---|
| Methods | Abstract **and** concrete | Only abstract (pure virtual) |
| Data members | Allowed | Typically none |
| Instantiation | Not directly | Not directly |
| Purpose | Shared base + contract | Pure contract |

A `const` pure virtual function requires implementations not to modify object state.

> **Always declare a `virtual` destructor in a polymorphic base class** — otherwise `delete basePtr;` on a derived object causes undefined behavior (the derived destructor won't run).

---

## 18. SOLID Principles (added detail)

| Principle | Idea |
|---|---|
| **S** — Single Responsibility | A class should have one reason to change (one responsibility). |
| **O** — Open/Closed | Open for extension, closed for modification (extend via new subclasses, not editing existing code). |
| **L** — Liskov Substitution | Subtypes must be substitutable for their base types without breaking correctness. |
| **I** — Interface Segregation | Many small, specific interfaces beat one large general-purpose interface. |
| **D** — Dependency Inversion | Depend on abstractions, not concretions. |

```cpp
// Single Responsibility: separate concerns
class Invoice { /* data + business rules only */ };
class InvoicePrinter { public: void print(const Invoice&); };   // printing
class InvoiceRepository { public: void save(const Invoice&); }; // persistence

// Open/Closed + Dependency Inversion: depend on the abstraction
class Discount { public: virtual double apply(double) const = 0; virtual ~Discount() = default; };
class NoDiscount      : public Discount { public: double apply(double p) const override { return p; } };
class SeasonalDiscount: public Discount { public: double apply(double p) const override { return p * 0.9; } };
class Checkout {
    const Discount& d;                       // abstraction, not a concrete class
public:
    Checkout(const Discount& d) : d(d) {}
    double total(double price) { return d.apply(price); }
};

// Liskov: a Square that breaks setWidth/setHeight invariants is NOT a valid Rectangle subtype.
// Interface Segregation: split a fat "Machine" into Printer, Scanner, Fax interfaces.
```

---

## 19. Interview Q&A (from the source, expanded)

### Implementing polymorphism without native support (C-like languages)
1. **Function pointers** — store and invoke functions dynamically:
   ```c
   typedef void (*FunctionPointer)();
   FunctionPointer fp = functionA; fp();   // calls functionA
   ```
2. **Structs with function pointers** — associate behavior with a struct (mimics methods):
   ```c
   typedef struct { void (*speak)(); } Animal;
   Animal dog = { dogSpeak }; dog.speak();   // "Woof!"
   ```
3. **Manual vtables** — a struct holds a pointer to a table of function pointers (mimics C++ vtables):
   ```c
   typedef struct { void (*speak)(); } AnimalVTable;
   typedef struct { AnimalVTable* vtable; } Animal;
   Animal dog = { &dogVTable }; dog.vtable->speak();   // "Woof!"
   ```

### Abstract classes vs. interfaces
An **interface** is a pure contract (methods to implement, no implementation). An **abstract class** can have both abstract and implemented methods and cannot be instantiated.

### Shallow vs. deep copy
Shallow copies only top-level structure (shared references to mutable nested objects → changes affect both). Deep copy recursively copies all levels (independent), more resource-intensive.

### What is an invariant?
A condition that remains true throughout an object's lifecycle. **Class invariants** hold for any instance (a bank account balance never negative); **object invariants** hold for a specific object (a rectangle's width/height always non-negative). They aid debugging, promote encapsulation, and maintain correctness.

### Multiple inheritance — construction/destruction order
Base classes are constructed **first**, **left to right** in the inheritance list, then the derived class. Destruction is the **reverse**: derived first, then base classes in reverse order of construction.

### RAII (Resource Acquisition Is Initialization)
A core C++ idiom: tie resource management to object lifetime — **acquire** resources in the constructor, **release** them in the destructor. Because destructors run automatically when objects go out of scope (even on exceptions), resources are reliably released, preventing leaks and eliminating explicit cleanup code. Examples: smart pointers (dynamic memory), file/lock guards (`std::lock_guard`, `std::fstream`).

### Object slicing
When a derived object is **copied into a base object by value**, the derived class's unique members/methods are "sliced off" — only the base part remains.

```cpp
Derived derivedObj(10, 20);
Base baseObj = derivedObj;     // slicing — derivedData lost
```

**Avoid it** by using **pointers or references** to the base, and making base methods **virtual**:

```cpp
Base& baseRef = derivedObj;    // no slicing
Base* basePtr = &derivedObj;   // no slicing
basePtr->show();               // calls Derived::show() if virtual
```

### Aggregation vs. composition
| Aspect | Aggregation | Composition |
|---|---|---|
| Ownership/lifespan | Part can exist independently | Part owned exclusively; lifecycle tied to whole |
| Strength | Weaker (independent objects) | Stronger (part can't exist without whole) |
| UML | **Empty** diamond | **Filled** diamond |

---

## 20. Low-Level Design (LLD) Patterns

Patterns categorized as **Structural**, **Behavioral**, and **Creational**, widely used in scalable systems.

1. **Decorator (Structural)** — attach responsibilities to an object dynamically; a flexible alternative to subclassing. *App:* data-processing pipelines — wrap a base text processor with tokenization, stop-word removal, stemming decorators, applied sequentially.

2. **Adapter (Structural)** — convert one interface into another clients expect, letting incompatible classes work together. *App:* wrap a legacy XML market-data feed to output standard JSON.
   ```java
   interface MarketDataFeed { double getLatestPrice(String ticker); }
   class LegacyFeed { float fetchPriceXML(String symbolCode) { return 150.5f; } }
   class FeedAdapter implements MarketDataFeed {
       private LegacyFeed legacyFeed;
       public double getLatestPrice(String ticker) { return (double) legacyFeed.fetchPriceXML(ticker); }
   }
   ```

3. **Strategy (Behavioral)** — define a family of interchangeable algorithms; the algorithm varies independently of clients. *App:* a trading agent hot-swaps alpha logic (mean-reversion vs. momentum) by volatility. Roles: **Context** (holds a Strategy), **Strategy** (interface), **ConcreteStrategy** (implementation).

4. **Singleton (Creational)** — ensure one instance with a global access point. *App:* a single thread-safe DB connection pool. *Pros:* instance control, lazy init. *Cons:* acts like a global, hard to unit-test/mock.

5. **Observer (Behavioral)** — one-to-many dependency: when the subject changes, all observers are notified. *App:* event-driven architectures / message brokers.
   ```java
   interface Observer { void update(String data); }
   class Subject {
       private List<Observer> observers = new ArrayList<>();
       public void attach(Observer o) { observers.add(o); }
       public void notifyAll(String data) { for (Observer o : observers) o.update(data); }
   }
   ```

6. **Factory Method & Abstract Factory (Creational)** — define an interface for creating objects, letting subclasses decide the concrete class; Abstract Factory groups related factories. *App:* spin up TCP or local-socket handlers based on runtime topology. *Factory Method* creates one type; *Abstract Factory* creates families (matching Buttons/Checkboxes/Scrollbars per OS).

7. **Command (Behavioral)** — encapsulate a request as an object → parameterize clients, queue/log requests, support undo. *App:* `TradeCommand` objects pushed to a queue for async execution, logging, and rollback.

8. **Builder (Creational)** — separate construction of a complex object from its representation; avoids the telescoping-constructor anti-pattern. *App:* configuring a model with dozens of hyperparameters via chained methods.
   ```java
   ModelPipeline pipeline = new ModelPipeline.Builder()
       .withTokenizer("BytePairEncoding")
       .withMaxSequenceLength(1024)
       .withAttentionHeads(12)
       .build();
   ```

### Additional Patterns (named for completeness)
- **Template Method (Behavioral)** — define an algorithm skeleton in a base method, deferring steps to subclasses.
- **Proxy (Structural)** — a surrogate controlling access (lazy loading, access control, remote proxy).
- **Facade (Structural)** — a unified high-level interface over a complex subsystem.
- **Composite (Structural)** — treat individual objects and compositions uniformly (tree structures).
- **Iterator (Behavioral)** — sequential access to elements without exposing the underlying representation.

---

## 21. Modern C++ Supplement: Move Semantics and the Rule of Five

When a class manages a resource, define (or `= default`/`= delete`) all five special members — the **Rule of Five**: destructor, copy constructor, copy assignment, **move constructor**, **move assignment**.

- **lvalue** — has a name/address; **rvalue** — a temporary. An **rvalue reference** `T&&` binds to temporaries.
- **Move semantics** transfer ownership of resources from a temporary instead of copying — cheap (steal the pointer, null out the source).

```cpp
class Buffer {
    int* data; size_t n;
public:
    Buffer(size_t n) : data(new int[n]), n(n) {}
    ~Buffer() { delete[] data; }
    Buffer(const Buffer& o) : data(new int[o.n]), n(o.n) {        // deep copy
        std::copy(o.data, o.data + n, data);
    }
    Buffer(Buffer&& o) noexcept : data(o.data), n(o.n) {          // move: steal
        o.data = nullptr; o.n = 0;
    }
    Buffer& operator=(Buffer&& o) noexcept {                      // move assign
        if (this != &o) { delete[] data; data = o.data; n = o.n; o.data = nullptr; o.n = 0; }
        return *this;
    }
};
```

`std::move` casts an lvalue to an rvalue to enable moving. Prefer smart pointers (`std::unique_ptr`, `std::shared_ptr`) which implement RAII and correct move semantics for you (**Rule of Zero**).

### Casting in a Polymorphic Context
- **`static_cast`** — compile-time conversion (up/down-casts without runtime check; unsafe down-cast if wrong).
- **`dynamic_cast`** — runtime-checked down-cast for polymorphic types; returns `nullptr` (pointers) or throws (references) on failure.
- `const_cast` (add/remove const), `reinterpret_cast` (low-level bit reinterpretation — avoid).

```cpp
Base* b = new Derived();
Derived* d = dynamic_cast<Derived*>(b);   // checked; nullptr if not a Derived
```

---

## 22. Quick Interview Checklist

- **Empty class** `sizeof == 1`; a vptr adds one pointer to objects of polymorphic classes.
- **Members initialize in declaration order**; const/reference/no-default-ctor members **require** the initializer list.
- **Rule of Three/Five/Zero:** manage copy/move/destroy consistently or let smart pointers do it.
- **Virtual dispatch** uses a per-class **vtable** + per-object **vptr**; always give polymorphic bases a **virtual destructor**.
- **Overloading** = compile-time; **overriding** (`virtual`) = runtime.
- **Diamond problem** → virtual inheritance (single shared base).
- **Slicing** happens on copy-by-value; use base pointers/references.
- **Composition (has-a)** is usually preferable to inheritance (is-a).
- **SOLID** keeps designs extensible and testable.
