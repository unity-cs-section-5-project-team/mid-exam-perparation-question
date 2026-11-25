# Window Programming - Pre-Mid Exam Mockup Answers

## Team Members

| Name | ID |
|------|----|
| Betel Gedamu | UUR00800/17 |
| Haset Tarekegn | UUR01528/16 |
| Tigist Muche | UUR00473/16 |
| Tihut Samuel | UUR00795/17 |

---

## Part 1: Software Development (Chapter 1)

### 1. Choosing the right SDLC model

We chose **B**.

**(B) Food-delivery app for a startup: Agile/Scrum (or RAD)**

- The market changes fast, so the requirements keep changing. We learn what users want as they use the app.
- The risk is lower. A small bug is not a disaster, and being first in the market is more important.
- Agile works in short sprints, so we can release often and change things quickly.
- Testing is done all the time in every sprint, not only at the end.

| | Aircraft (A) | Food app (B) |
|---|---|---|
| Requirements | Stable | Keep changing |
| Risk | Very high | Lower |
| Testing | Planned and very strict | Continuous, every sprint |
| Model | Waterfall / Spiral | Agile/Scrum / RAD |

### 2. The hidden trade-offs of RAD

RAD is fast because it uses prototypes and does very little planning at the start. But this causes problems later:

- **Architectural debt:** prototypes are built quickly with shortcuts. If we keep the prototype as the final system, the weak design stays and it becomes hard to add new features.
- **Scalability problem:** a prototype is made to show features, not to handle many users or a lot of data. Nobody planned for growth.
- **No deep risk analysis:** risks like security and performance problems are not studied early. They show up later when fixing them is expensive.
- **Reliability:** big systems need to be available all the time (high availability). With short time-boxed delivery there is not enough time for careful design and testing.

That is why RAD is not good for large or mission-critical systems.

### 3. Balancing development principles

Yes, trying too hard to follow one principle can break another.

- **DRY:** don't write the same code twice.
- **KISS:** keep it simple.
- **YAGNI:** don't build something you don't need now.
- **Separation of Concerns:** divide the program into parts, each with its own job.

**Example of over-engineering:** a program that only adds two numbers. A developer who tries too hard to follow DRY and Separation of Concerns makes:

- an `IOperation` interface,
- a base class,
- an `AdditionClass`,
- a factory class,
- a service class and a controller class

just to do `a + b`. This breaks KISS because it is much more complicated than needed. It also breaks YAGNI because the extra classes are made for other operations that nobody asked for.

Too much DRY can also be a problem. If two functions look similar but are for different features and we merge them into one big function with many `if` conditions, a change for one feature can break the other.

**Conclusion:** these principles are balances, not strict rules. We should use them in a way that fits the size of the project.

### 4. The economics of testing and code quality

**Why fixing an error is cheapest early and most expensive after deployment:**

- If the mistake is found in the requirements or design stage, we only change a document or a diagram.
- If it is found during coding, we change the code.
- If it is found in late testing, we change the code and then test everything again.
- If it is found after deployment, the users are already affected. We need an emergency fix, a new release, and the company may lose customers and its good name.
- Also, early mistakes grow. Everything built after a bad design depends on it, so more and more work must be redone.

**How TDD, CI and code reviews keep the cost low:**

- **TDD (Test-Driven Development):** we write the tests before the code. Every part is tested as soon as it is written, so bugs are found early.
- **Continuous Integration:** the code from all developers is merged and tested automatically many times a day, so problems are found quickly.
- **Peer code reviews:** other developers read our code before it is accepted. They catch mistakes and bad design early, when it is cheap to fix.

---

## Part 2: .NET Architecture and C# (Chapter 2)

### 5. MSIL, JIT and the platform independence paradox

**How it works:**

- The C# code is compiled into MSIL, which is not tied to any CPU.
- When the program runs, the JIT compiler changes the MSIL into machine code for the CPU of that computer.

**Why .NET Framework apps are still platform-dependent:**

MSIL is independent of the CPU, but that does not mean it is independent of the operating system. The parts needed to run it were made only for Windows:

- The CLR (runtime) of .NET Framework only worked on Windows.
- The class libraries (like Windows Forms) use Windows-specific things like Win32 APIs and the registry.

So the code is portable, but what it needs to run is only on Windows.

**How Mono changed it:**

Mono is a third-party, open-source version of the .NET runtime and libraries. It has its own CLR and JIT that work on Linux, macOS and other systems. This allows the same MSIL code to run on other platforms, not only Windows.

### 6. Managed vs. unmanaged code and language interoperability

**How managed code protects a program:**

Managed code runs inside the CLR, which gives us services that unmanaged (Win32) code does not have:

- **Garbage collection:** unused memory is cleaned automatically, so there are no memory leaks.
- **Type safety:** the CLR makes sure data is used as its correct type.
- **Array bounds checking:** if the code tries to go outside of an array, it is stopped. This prevents memory corruption.
- **Metadata:** the assembly describes its types, so the CLR can check the code before running it.

In unmanaged code the programmer must manage memory by himself, and mistakes cause crashes and security problems.

**How MSIL and the CTS let C# and VB.NET work together:**

- All .NET languages compile into the same MSIL, so the CLR sees the same kind of code no matter which language we used.
- The CTS (Common Type System) gives all languages the same data types. For example, `string` in C# and `String` in VB.NET both become `System.String`, which comes from `System.Object`.
- Because of this, one part of an application can be written in C# and another part in VB.NET, and they can share data without conflicts.

### 7. Solving "DLL Hell"

**The problem:** before .NET, applications shared DLLs. When a new application was installed, it could replace a shared DLL with a different version, and the old applications that needed the old version stopped working.

**How .NET solves it:**

- **Assemblies:** the unit of deployment. An application can keep its own private assembly in its own folder, so no other app can overwrite it.
- **Manifest and metadata:** every assembly has a manifest that lists its name, its version, and the versions of the other assemblies it needs. The CLR uses it to load the correct versions.
- **Strong names:** a strong name (name, version, culture and a public key) gives the assembly a unique identity, so two different assemblies can't be mixed up.
- **GAC (Global Assembly Cache):** a place to keep shared assemblies. Many versions of the same assembly can live there side by side, and each application uses the version it needs.

**Why namespace and assembly are not the same thing:**

- A namespace is a logical grouping of classes (for example `System.Windows.Forms`). It helps us organize code and avoid name conflicts.
- An assembly is a physical grouping. It is the actual `.exe` or `.dll` file we deploy.
- One assembly can have many namespaces, and one namespace can be in many assemblies.

### 8. ADO.NET vs. Entity Framework and LINQ

**Why raw ADO.NET was error-prone:**

- We must open and close the database connection ourselves. If we forget to close it, there are leaks and the app becomes slow.
- Queries are written as SQL strings, so a spelling mistake is not found when compiling. It only fails when the program runs.
- Data comes as DataSets, and we must change it into objects by hand. This is repetitive, and mistakes with types and null values are common.
- We write a lot of the same code again and again.

**What we gain with Entity Framework (ORM):**

- It automatically changes database tables into .NET objects and back, so we work with classes and properties instead of rows and columns.
- It manages the connections and the conversion for us.
- LINQ gives one standard way to query SQL databases, XML and collections, and the compiler checks it.
- Less code, fewer errors and faster development.

**What we give up (trade-off):**

- Less control over the exact SQL that is created.
- It can be slower for big or complex queries.
- We need to learn the framework, and it is harder to find bugs because the SQL is hidden.

### 9. Under the hood of event-driven programming

**Console app vs. Windows Forms app:**

- A console app runs from `Main()` line by line until it finishes and then exits.
- A Windows Forms app starts a main loop (`Application.Run`) that keeps running and waits for the user to do something.

**Terms:**

- **Main loop (message loop):** keeps waiting for and handling messages from the operating system.
- **Event:** something that happens, like a button Click.
- **Event listener:** code that is subscribed to an event and waits for it.
- **Delegate:** a type-safe reference to a method. Events work using delegates.
- **Lambda expression:** a short way to write a small method directly in the code.
- **Event handler:** the method that runs when the event happens.

**What happens from the click to our code running:**

1. The user clicks a Button on the form.
2. The operating system sees the click and puts a message in the application's message queue.
3. The main loop takes the message and sends it to the Button.
4. The Button raises its Click event.
5. The delegate of the event holds the list of subscribed methods and calls them.
6. Our event handler (for example `button1_Click`) runs.
7. After the handler finishes, the program goes back to the main loop and waits for the next event.

**Example code:**

```csharp
public Form1()
{
    InitializeComponent();

    // subscribe the handler to the Click event
    button1.Click += button1_Click;

    // or with a lambda expression
    // button1.Click += (sender, e) => MessageBox.Show("Clicked!");
}

private void button1_Click(object sender, EventArgs e)
{
    MessageBox.Show("Button was clicked!");
}
```
