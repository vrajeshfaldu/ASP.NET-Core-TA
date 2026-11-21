# Learning C# Concepts 1–5

A beginner-friendly guide for B.Tech Computer Engineering students. It introduces C# basics, control statements, methods, arrays and collections, and object-oriented programming (OOP), with short examples, expected output, practice questions, and viva answers.

> **How to run the examples:** Create a Console App in Visual Studio or run `dotnet new console` in a folder and use `dotnet run`. Most snippets are statements to place inside `Main()` unless they show a complete class or program. `using` directives go at the top of a file. C# is case-sensitive: `WriteLine` and `writeline` are different names.

## Contents

1. [C# Basics](#1-c-basics)
2. [Control Statements](#2-control-statements)
3. [Methods](#3-methods)
4. [Arrays and Collections](#4-arrays-and-collections)
5. [Object-Oriented Programming](#5-object-oriented-programming-oop)
6. [Common Beginner Mistakes](#common-beginner-mistakes)
7. [Important Differences](#important-differences)
8. [Practice Programs](#practice-programs)
9. [Viva Questions](#viva-questions)
10. [Quick Revision](#quick-revision)

---

# 1. C# Basics

## 1.1 What is C#?

C# (pronounced **C-sharp**) is a programming language developed by Microsoft. It is commonly used with .NET to create console, web, desktop, cloud, mobile, and game applications. A **programming language** lets us give instructions to a computer.

## 1.2 Features of C#

- **Readable:** Its syntax is designed to be understandable.
- **Strongly typed:** Variables have types, and C# checks that operations use compatible types.
- **Object-oriented:** Programs can organize data and behavior into classes and objects.
- **Managed by .NET:** The .NET runtime helps execute programs and manage memory.
- **Large standard library:** .NET provides ready-made types for text, collections, files, networking, and more.
- **Cross-platform:** .NET applications can run on supported versions of Windows, Linux, and macOS.

## 1.3 Basic program structure and `Main()`

A program contains statements. In the traditional structure below, `Main()` is the **entry point**: execution begins there. `static` lets the runtime call `Main()` without first creating a `Program` object. `void` means it returns no value.

### Syntax

```csharp
using System;

class Program
{
    static void Main(string[] args)
    {
        // Statements run here
    }
}
```

### Example

```csharp
using System;

class Program
{
    static void Main(string[] args)
    {
        Console.WriteLine("Hello, C#!");
    }
}
```

**Output**

```text
Hello, C#!
```

**Explanation:** `using System;` makes common .NET types such as `Console` available without writing their full names. `class Program` declares a class. `Main()` contains the first instructions executed. `Console.WriteLine()` displays a line of text.

> Newer C# projects can use top-level statements and omit an explicitly written `Main()`. The traditional form above makes the program structure visible to beginners.

## 1.4 Variables and constants

A **variable** is a named place to store a value that may change. A **constant** is a named value that cannot be changed after it is declared.

### Syntax

```csharp
int age = 18;
const double Pi = 3.14159;
```

### Example

```csharp
int score = 70;
score = 85;
const int PassingMarks = 40;

Console.WriteLine(score);
Console.WriteLine(PassingMarks);
```

**Output**

```text
85
40
```

**Explanation:** `score` is updated from `70` to `85`. `PassingMarks` is fixed. Assigning a new value to a `const` variable causes a compile-time error.

## 1.5 Data types

A data type tells C# what kind of value a variable stores. The following are common built-in types.

| Type | Stores | Example literal |
|---|---|---|
| `int` | Whole numbers | `42` |
| `float` | Approximate decimal numbers, with less precision than `double` | `3.14f` |
| `double` | Approximate decimal numbers; common for general calculations | `3.14` |
| `decimal` | High-precision base-10 decimal values, often used for money | `19.99m` |
| `char` | One UTF-16 character, in single quotes | `'A'` |
| `string` | Text, in double quotes | `"Hello"` |
| `bool` | `true` or `false` | `true` |

### Example

```csharp
int count = 12;
float temperature = 21.5f;
double distance = 1234.567;
decimal price = 49.95m;
char grade = 'A';
string studentName = "Riya";
bool isEnrolled = true;

Console.WriteLine($"{studentName}: grade {grade}, enrolled = {isEnrolled}");
Console.WriteLine($"Count = {count}, temperature = {temperature}, distance = {distance}, price = {price}");
```

**Output**

```text
Riya: grade A, enrolled = True
Count = 12, temperature = 21.5, distance = 1234.567, price = 49.95
```

**Explanation:** Each variable has a type that fits its value. The `f` suffix marks a `float` literal and the `m` suffix marks a `decimal` literal. `$"...{value}..."` is string interpolation: it inserts values into text.

## 1.6 Type casting and conversion

A **conversion** changes a value from one type to another. An **implicit conversion** is automatic when the conversion is safe for the destination type. An **explicit conversion** is written by the programmer, usually because information may be lost.

### Implicit conversion

```csharp
int wholeNumber = 25;
double approximateNumber = wholeNumber;
Console.WriteLine(approximateNumber);
```

**Output:** `25`

**Explanation:** An `int` can be converted to `double` automatically because `double` can represent a much wider range of values.

### Explicit conversion (cast)

```csharp
double measurement = 9.8;
int roundedDown = (int)measurement;
Console.WriteLine(roundedDown);
```

**Output:** `9`

**Explanation:** `(int)` requests an explicit conversion. The fractional part is discarded; this cast does not round to the nearest integer.

### Convert user input

```csharp
string text = "42";
int number = Convert.ToInt32(text);
Console.WriteLine(number + 1);
```

**Output:** `43`

**Explanation:** Text read from a console is a string. `Convert.ToInt32()` converts valid integer text to an `int`. Invalid text can cause a `FormatException`; for user input, `int.TryParse()` is often a safer choice.

## 1.7 Operators

Operators perform actions on values.

### Arithmetic operators

`+` adds, `-` subtracts, `*` multiplies, `/` divides, and `%` gives the remainder.

```csharp
int a = 10;
int b = 3;
Console.WriteLine(a + b); // 13
Console.WriteLine(a - b); // 7
Console.WriteLine(a * b); // 30
Console.WriteLine(a / b); // 3: integer division
Console.WriteLine(a % b); // 1: remainder
```

**Explanation:** When both operands are integers, `/` performs integer division and drops the fractional part. Use a floating-point operand, such as `10.0 / 3`, when a fractional result is needed.

### Relational operators

`==`, `!=`, `>`, `<`, `>=`, and `<=` compare values. A comparison produces `true` or `false`.

```csharp
int age = 20;
Console.WriteLine(age >= 18); // True
Console.WriteLine(age == 20); // True
Console.WriteLine(age != 20); // False
```

### Logical operators

`&&` means AND, `||` means OR, and `!` means NOT. They combine or reverse Boolean conditions.

```csharp
int age = 20;
bool hasId = true;
Console.WriteLine(age >= 18 && hasId); // True
Console.WriteLine(age < 18 || hasId);  // True
Console.WriteLine(!hasId);             // False
```

### Assignment operators

`=` assigns a value. Compound forms update a variable using its current value: `+=`, `-=`, `*=`, `/=`, `%=`.

```csharp
int total = 10;
total += 5; // Same as total = total + 5
Console.WriteLine(total); // 15
```

### Increment and decrement

`++` adds one and `--` subtracts one. Prefix (`++i`) changes the value before the expression is evaluated; postfix (`i++`) changes it after the current expression is evaluated.

```csharp
int i = 4;
Console.WriteLine(i++); // 4, then i becomes 5
Console.WriteLine(++i); // i becomes 6, then prints 6
```

## 1.8 Console input and output

Use `Console.Write()` or `Console.WriteLine()` for output and `Console.ReadLine()` for text input.

### Example

```csharp
Console.Write("Enter your name: ");
string name = Console.ReadLine() ?? "Student";
Console.WriteLine($"Hello, {name}!");
```

**Sample run**

```text
Enter your name: Aman
Hello, Aman!
```

**Explanation:** `ReadLine()` reads a line of text. `?? "Student"` provides a fallback if the input is `null`. To read a number, convert the text or use `TryParse()` (see [type conversion](#16-type-casting-and-conversion)).

## 1.9 Comments

Comments are notes for people reading the source code. The compiler does not execute them.

```csharp
// This is a one-line comment.
int score = 90;

/* This is a
   multi-line comment. */
Console.WriteLine(score);
```

**Explanation:** Use `//` for a short comment and `/* ... */` for a comment spanning lines. Comments should explain intent, not repeat obvious code.

---

# 2. Control Statements

Control statements decide which code runs and how many times it runs. The examples below are statements to put inside `Main()`.

## 2.1 `if`

**When to use:** Run a block only when a condition is true.

**Syntax**

```csharp
if (condition)
{
    // Runs when condition is true
}
```

**Example**

```csharp
int temperature = 32;
if (temperature > 30)
{
    Console.WriteLine("It is warm.");
}
```

**Output:** `It is warm.`

**Explanation:** `temperature > 30` is true, so the body runs.

## 2.2 `if-else`

**When to use:** Choose between two alternatives.

**Syntax**

```csharp
if (condition)
{
    // true branch
}
else
{
    // false branch
}
```

**Example**

```csharp
int number = 7;
if (number % 2 == 0)
    Console.WriteLine("Even");
else
    Console.WriteLine("Odd");
```

**Output:** `Odd`

**Explanation:** The remainder after dividing 7 by 2 is not zero, so the `else` branch runs.

## 2.3 `else-if`

**When to use:** Test multiple conditions in order. The first true branch runs.

**Syntax**

```csharp
if (condition1)
{
}
else if (condition2)
{
}
else
{
}
```

**Example**

```csharp
int marks = 76;
if (marks >= 90)
    Console.WriteLine("A");
else if (marks >= 70)
    Console.WriteLine("B");
else
    Console.WriteLine("C");
```

**Output:** `B`

**Explanation:** The first test is false, and the second is true. Conditions are checked from top to bottom.

## 2.4 Nested `if`

**When to use:** Check a condition only after another condition is satisfied. Avoid deep nesting when a simpler condition is clear.

**Syntax**

```csharp
if (outerCondition)
{
    if (innerCondition)
    {
        // Both conditions are true
    }
}
```

**Example**

```csharp
int age = 19;
bool hasId = true;
if (age >= 18)
{
    if (hasId)
        Console.WriteLine("Entry allowed");
}
```

**Output:** `Entry allowed`

**Explanation:** The inner check is reached because `age >= 18` is true, and `hasId` is also true.

## 2.5 `switch`

**When to use:** Select one branch by comparing a value with several fixed choices.

**Syntax**

```csharp
switch (value)
{
    case choice1:
        // statements
        break;
    default:
        // no matching choice
        break;
}
```

**Example**

```csharp
int day = 2;
switch (day)
{
    case 1:
        Console.WriteLine("Monday");
        break;
    case 2:
        Console.WriteLine("Tuesday");
        break;
    default:
        Console.WriteLine("Unknown day");
        break;
}
```

**Output:** `Tuesday`

**Explanation:** `day` matches `case 2`. `break` exits the switch so another case does not run. `default` handles unmatched values.

## 2.6 `for`

**When to use:** Repeat a block a known number of times or when a counter is useful.

**Syntax**

```csharp
for (initialization; condition; update)
{
    // repeated statements
}
```

**Example**

```csharp
for (int i = 1; i <= 3; i++)
{
    Console.WriteLine(i);
}
```

**Output**

```text
1
2
3
```

**Explanation:** `i` begins at 1, the condition is checked before each iteration, and `i++` runs after each iteration.

## 2.7 `while`

**When to use:** Repeat while a condition is true when the number of repetitions may not be known in advance.

**Syntax**

```csharp
while (condition)
{
    // repeated statements; update condition here
}
```

**Example**

```csharp
int count = 1;
while (count <= 3)
{
    Console.WriteLine(count);
    count++;
}
```

**Output**

```text
1
2
3
```

**Explanation:** The condition is checked before the body. Updating `count` is important; without it, this example would loop forever.

## 2.8 `do-while`

**When to use:** Run a block at least once, then repeat while the condition is true.

**Syntax**

```csharp
do
{
    // statements
}
while (condition);
```

**Example**

```csharp
int count = 1;
do
{
    Console.WriteLine(count);
    count++;
}
while (count <= 3);
```

**Output**

```text
1
2
3
```

**Explanation:** Unlike `while`, the condition is checked after the body, so the body always runs at least once.

## 2.9 `foreach`

**When to use:** Visit every item in an array or collection when you do not need to manage an index.

**Syntax**

```csharp
foreach (Type item in collection)
{
    // use item
}
```

**Example**

```csharp
string[] colors = { "Red", "Green", "Blue" };
foreach (string color in colors)
{
    Console.WriteLine(color);
}
```

**Output**

```text
Red
Green
Blue
```

**Explanation:** `color` receives each array value in order. A `foreach` loop is convenient for reading items; use an indexed loop when you need the position or need to replace array elements.

## 2.10 `break`

**When to use:** Exit the nearest loop or `switch` immediately.

**Syntax and example**

```csharp
for (int i = 1; i <= 5; i++)
{
    if (i == 3)
        break;
    Console.WriteLine(i);
}
```

**Output**

```text
1
2
```

**Explanation:** When `i` becomes 3, `break` exits the loop before printing 3.

## 2.11 `continue`

**When to use:** Skip the rest of the current loop iteration and move to the next one.

**Syntax and example**

```csharp
for (int i = 1; i <= 5; i++)
{
    if (i == 3)
        continue;
    Console.WriteLine(i);
}
```

**Output**

```text
1
2
4
5
```

**Explanation:** The iteration for 3 is skipped. The loop then continues with 4.

---

# 3. Methods

## 3.1 What is a method and why use one?

A **method** is a named group of statements that performs a task. Methods let us reuse code, divide a large task into smaller parts, and give actions meaningful names. In C#, a method is declared inside a class or other type.

## 3.2 Method syntax

```csharp
accessModifier staticOrInstance returnType MethodName(parameterList)
{
    // method body
    return value; // only when the return type is not void
}
```

- **Return type:** Type of result, or `void` when there is no result.
- **Parameters:** Named inputs listed in the declaration.
- **Arguments:** Actual values supplied when calling the method.
- **Method call:** Runs the method.

## 3.3 Method without parameters

**When to use:** The method needs no information from its caller.

```csharp
static void ShowMessage()
{
    Console.WriteLine("Welcome to C#!");
}

ShowMessage();
```

**Output:** `Welcome to C#!`

**Explanation:** `ShowMessage` has no parameters and uses `void`, so it performs an action but returns no value. This snippet is placed inside a class; call it from `Main()`.

## 3.4 Method with parameters

**When to use:** The method needs values from its caller.

```csharp
static void Greet(string name)
{
    Console.WriteLine($"Hello, {name}!");
}

Greet("Asha");
```

**Output:** `Hello, Asha!`

**Explanation:** `name` is a parameter. `"Asha"` is the argument passed to it.

## 3.5 Return values and `void`

A method with a non-`void` return type must return a compatible value. A `void` method does not return a value to its caller.

```csharp
static int Square(int number)
{
    return number * number;
}

int answer = Square(4);
Console.WriteLine(answer);
```

**Output:** `16`

**Explanation:** `int` is the return type, and `return` sends `16` back to the caller. `answer` stores it.

## 3.6 Methods with and without parameters: complete example

```csharp
using System;

class Program
{
    static void Main()
    {
        ShowTitle();
        PrintSum(4, 6);
        int product = Multiply(4, 6);
        Console.WriteLine(product);
    }

    static void ShowTitle()
    {
        Console.WriteLine("Methods");
    }

    static void PrintSum(int first, int second)
    {
        Console.WriteLine(first + second);
    }

    static int Multiply(int first, int second)
    {
        return first * second;
    }
}
```

**Output**

```text
Methods
10
24
```

**Explanation:** `ShowTitle()` has no inputs. `PrintSum()` has inputs but returns nothing. `Multiply()` accepts inputs and returns an `int`.

## 3.7 Method overloading

**Definition:** Overloading means declaring methods with the same name but different parameter lists (different number, types, or order of parameters).

**Why use it:** The same operation can work with different kinds or amounts of input.

```csharp
static int Add(int a, int b) => a + b;
static int Add(int a, int b, int c) => a + b + c;

Console.WriteLine(Add(2, 3));
Console.WriteLine(Add(2, 3, 4));
```

**Output**

```text
5
9
```

**Explanation:** C# chooses which overload to call based on the arguments. Changing only the return type is not enough to create a valid overload.

## 3.8 Optional/default parameters

**Definition:** A parameter with a default value may be omitted by the caller. Optional parameters must follow required parameters.

```csharp
static void Welcome(string name, string greeting = "Hello")
{
    Console.WriteLine($"{greeting}, {name}!");
}

Welcome("Mina");
Welcome("Mina", "Good morning");
```

**Output**

```text
Hello, Mina!
Good morning, Mina!
```

**Explanation:** The first call uses the default greeting. The second supplies a different argument.

## 3.9 `ref` parameters

**Definition:** `ref` passes a variable by reference, allowing the method to change the caller's existing variable. The variable must be assigned before the call, and `ref` must appear both in the method declaration and the call.

```csharp
static void AddOne(ref int value)
{
    value++;
}

int number = 5;
AddOne(ref number);
Console.WriteLine(number);
```

**Output:** `6`

**Explanation:** The method updates the same variable `number`, not a separate copy.

## 3.10 `out` parameters

**Definition:** `out` lets a method return an additional value through a parameter. The method must assign the `out` parameter before it finishes; the caller does not need to assign it first.

```csharp
static void GetCoordinates(out int x, out int y)
{
    x = 10;
    y = 20;
}

GetCoordinates(out int pointX, out int pointY);
Console.WriteLine($"{pointX}, {pointY}");
```

**Output:** `10, 20`

**Explanation:** The method assigns both outputs. `out` is also commonly used by parsing methods such as `int.TryParse()`.

---

# 4. Arrays and Collections

## 4.1 What is an array?

An **array** stores a fixed number of values of one type. Its elements are accessed by index, starting at index `0`.

### One-dimensional array

```csharp
int[] marks = { 75, 82, 91 };
Console.WriteLine(marks[0]); // 75
Console.WriteLine(marks.Length); // 3
```

**Explanation:** `marks[0]` accesses the first value. `Length` reports the number of elements.

### Multidimensional array

A rectangular multidimensional array has a fixed number of rows and columns.

```csharp
int[,] grid =
{
    { 1, 2, 3 },
    { 4, 5, 6 }
};
Console.WriteLine(grid[1, 2]); // 6
```

**Explanation:** `grid[1, 2]` means row index 1, column index 2; both indexes start at zero.

### Jagged array

A jagged array is an array whose elements are separate arrays. Rows may have different lengths.

```csharp
int[][] groups = new int[2][];
groups[0] = new int[] { 1, 2 };
groups[1] = new int[] { 3, 4, 5 };
Console.WriteLine(groups[1][2]); // 5
```

**Explanation:** `groups[1][2]` accesses the third value of the second row. Each inner array must be created before its elements are used.

### `for` and `foreach` with arrays

```csharp
int[] values = { 10, 20, 30 };

for (int i = 0; i < values.Length; i++)
    Console.WriteLine($"Index {i}: {values[i]}");

foreach (int value in values)
    Console.WriteLine(value);
```

**Explanation:** Use `for` when the index is needed. Use `foreach` when you only need each value.

## 4.2 Collections overview

Collections are types for storing groups of items. Unlike an array, many collections can change size as items are added or removed. Generic collections use a type parameter such as `List<int>`; this means the list stores integers.

Add this directive for the examples below:

```csharp
using System.Collections.Generic;
```

### `List<T>`

**What and when:** An ordered, resizable collection that allows duplicates. Use it when you need a sequence that may grow or shrink and need access by index.

**Syntax and operations**

```csharp
List<string> names = new List<string>();
names.Add("Asha");
names.Add("Ravi");
names.Add("Asha"); // duplicates are allowed

Console.WriteLine(names[0]);            // search/access by index: Asha
Console.WriteLine(names.Contains("Ravi")); // True
names.Remove("Ravi");                   // removes the first matching value

foreach (string name in names)
    Console.WriteLine(name);
```

**Output**

```text
Asha
True
Asha
Asha
```

**Explanation:** `Add()` appends an item, `Contains()` checks whether an item exists, and `Remove()` removes the first matching value. `Count` gives the number of items; `Clear()` removes all items; `RemoveAt(index)` removes the item at an index.

### `Dictionary<TKey, TValue>`

**What and when:** Stores key/value pairs. Each key must be unique. Use it to look up a value by a meaningful key, such as a student ID.

**Syntax and operations**

```csharp
Dictionary<int, string> students = new Dictionary<int, string>();
students.Add(101, "Asha");
students[102] = "Ravi"; // indexer adds a new key or updates an existing key

Console.WriteLine(students[101]);
Console.WriteLine(students.ContainsKey(102)); // True

if (students.TryGetValue(103, out string studentName))
    Console.WriteLine(studentName);
else
    Console.WriteLine("Student not found");

students.Remove(102);

foreach (KeyValuePair<int, string> student in students)
    Console.WriteLine($"{student.Key}: {student.Value}");
```

**Output**

```text
Asha
True
Student not found
101: Asha
```

**Explanation:** `Add(key, value)` adds a pair and throws an exception if the key already exists. `ContainsKey()` checks for a key. `TryGetValue()` safely searches and retrieves a value. `Remove(key)` removes the pair for that key. `Count` reports the number of pairs.

### `HashSet<T>`

**What and when:** Stores unique values; duplicate additions do not create duplicate entries. Use it when uniqueness and fast membership checks matter and you do not need index-based access.

**Syntax and operations**

```csharp
HashSet<int> first = new HashSet<int> { 1, 2, 3 };
first.Add(3); // set remains { 1, 2, 3 }
first.Add(4);
Console.WriteLine(first.Contains(2)); // True
first.Remove(1);

HashSet<int> second = new HashSet<int> { 3, 5 };
first.UnionWith(second); // add items from second, without duplicates

foreach (int value in first)
    Console.WriteLine(value);
```

**Possible output order**

```text
2
3
4
5
```

**Explanation:** A set does not provide an index and should not be used when a particular item order is required. `Add()` adds a unique item; `Remove()` removes an item; `Contains()` searches; `UnionWith()` combines this set with another set. Other useful methods include `IntersectWith()` and `ExceptWith()`.

### `Queue<T>`

**What and when:** A first-in, first-out (FIFO) collection: the first item added is the first removed. Use it for waiting lines and work to be processed in arrival order.

**Syntax and operations**

```csharp
Queue<string> queue = new Queue<string>();
queue.Enqueue("First");
queue.Enqueue("Second");

Console.WriteLine(queue.Peek());    // First; inspect without removing
Console.WriteLine(queue.Contains("Second")); // True
Console.WriteLine(queue.Dequeue()); // First; removes it
queue.Enqueue("Third");

foreach (string item in queue)
    Console.WriteLine(item);
```

**Output**

```text
First
True
First
Second
Third
```

**Explanation:** `Enqueue()` adds to the back, `Dequeue()` removes and returns the front item, and `Peek()` reads the front without removing it. `Contains()` searches; `Count` gives the number of items. `Dequeue()` and `Peek()` throw an exception if the queue is empty, so check `Count` first.

### `Stack<T>`

**What and when:** A last-in, first-out (LIFO) collection: the most recently added item is the first removed. Use it for undo actions, browser-history steps, or tracking nested work.

**Syntax and operations**

```csharp
Stack<string> stack = new Stack<string>();
stack.Push("Bottom");
stack.Push("Top");

Console.WriteLine(stack.Peek());    // Top; inspect without removing
Console.WriteLine(stack.Contains("Bottom")); // True
Console.WriteLine(stack.Pop());     // Top; removes it

foreach (string item in stack)
    Console.WriteLine(item);
```

**Output**

```text
Top
True
Top
Bottom
```

**Explanation:** `Push()` adds to the top, `Pop()` removes and returns the top item, and `Peek()` reads it without removal. `Contains()` searches; `Count` reports the number of items. Check `Count` before calling `Pop()` or `Peek()` on a possibly empty stack.

## 4.3 Common collection methods

| Method | Common use |
|---|---|
| `Add()` | Add an item to a `List<T>`, `HashSet<T>`, or key/value pair to a dictionary. Queues use `Enqueue()` and stacks use `Push()`. |
| `Remove()` | Remove a matching item from a list or set; for a dictionary, use `Remove(key)`. Queues and stacks remove from their ends with `Dequeue()` and `Pop()`. |
| `Contains()` | Check whether a list, set, queue, or stack contains an item. A dictionary's `Contains()` checks a key/value pair; use `ContainsKey()` to check for a key. |
| `ContainsKey()` | Check whether a key exists in a dictionary before using it. |
| `UnionWith()` | Add every item from another collection to a `HashSet<T>`, keeping the result unique. |

---

# 5. Object-Oriented Programming (OOP)

OOP organizes a program around **objects** that hold data and provide behavior. The four commonly discussed OOP ideas are encapsulation, inheritance, polymorphism, and abstraction.

> The code examples in this section are small, related snippets. Put each class at file/class level and statements that create objects inside `Main()`. Do not declare one class inside another unless specifically shown.

## 5.1 Class and object

**Definition:** A class is a blueprint describing data and behavior. An object is one instance created from that class.

**Why used:** Classes let us model related information and actions together. Different objects can hold different data while sharing the same class definition.

**Syntax**

```csharp
class ClassName
{
    // fields, properties, constructors, and methods
}

ClassName item = new ClassName();
```

**Example: Student class**

```csharp
class Student
{
    public string Name { get; set; } = "";
    public int RollNumber { get; set; }

    public void Display()
    {
        Console.WriteLine($"{RollNumber}: {Name}");
    }
}

Student student = new Student();
student.Name = "Asha";
student.RollNumber = 101;
student.Display();
```

**Output:** `101: Asha`

**Explanation:** `Student` is the class; `student` is an object. The object stores its own name and roll number. `new Student()` creates it, and the dot operator accesses its public members.

## 5.2 Fields

**Definition:** A field is a variable declared in a class. An instance field belongs to each object.

**Why used:** Fields hold the internal state or data of an object. They are often `private` so other code uses the class's public methods or properties to access the data safely.

**Syntax and example**

```csharp
class Faculty
{
    private int facultyId;

    public void SetId(int id)
    {
        facultyId = id;
    }

    public void DisplayId()
    {
        Console.WriteLine(facultyId);
    }
}

Faculty faculty = new Faculty();
faculty.SetId(501);
faculty.DisplayId();
```

**Output:** `501`

**Explanation:** `facultyId` is private, so code outside `Faculty` cannot access it directly. Public methods provide controlled access.

## 5.3 Properties

**Definition:** A property is a class member that provides controlled access to a value. It looks like a field when used, but can include validation or other logic.

**Why used:** Properties protect data while providing a convenient public way to read or change it. `get` reads; `set` assigns, and the assigned value is available as `value`.

**Syntax and example**

```csharp
class Employee
{
    private decimal salary;

    public decimal Salary
    {
        get { return salary; }
        set
        {
            if (value >= 0)
                salary = value;
        }
    }
}

Employee employee = new Employee();
employee.Salary = 50000m;
Console.WriteLine(employee.Salary);
```

**Output:** `50000`

**Explanation:** The setter prevents negative values from replacing the salary. A shorter auto-property is `public string Name { get; set; } = "";` when no custom logic is required.

## 5.4 Constructors and parameterized constructors

**Definition:** A constructor is a special member called when an object is created. A parameterized constructor accepts values to initialize the object.

**Why used:** Constructors make sure an object starts with useful initial values.

**Syntax and example**

```csharp
class Student
{
    public string Name { get; }
    public int RollNumber { get; }

    public Student(string name, int rollNumber)
    {
        Name = name;
        RollNumber = rollNumber;
    }
}

Student student = new Student("Ravi", 102);
Console.WriteLine($"{student.RollNumber}: {student.Name}");
```

**Output:** `102: Ravi`

**Explanation:** `Student(...)` has the same name as the class and no return type. The arguments supplied after `new` are assigned to the object's properties. `get`-only properties can be assigned in the constructor.

## 5.5 Encapsulation

**Definition:** Encapsulation groups data and methods together and limits direct access to an object's internal data.

**Why used:** It prevents invalid changes and keeps the rules for changing data in one place.

**Syntax and example: Employee account balance**

```csharp
class EmployeeAccount
{
    private decimal balance;

    public void Deposit(decimal amount)
    {
        if (amount > 0)
            balance += amount;
    }

    public decimal GetBalance()
    {
        return balance;
    }
}

EmployeeAccount account = new EmployeeAccount();
account.Deposit(100m);
Console.WriteLine(account.GetBalance());
```

**Output:** `100`

**Explanation:** The balance is private. Users of the class can deposit only a positive amount and read the balance through the provided members.

## 5.6 Inheritance

**Definition:** Inheritance lets a derived class receive accessible members from a base class. The colon (`:`) names the base class.

**Why used:** It shares common behavior between related types and allows a derived class to add specialized behavior.

**Syntax and example**

```csharp
class Employee
{
    public string Name { get; set; } = "";

    public void Work()
    {
        Console.WriteLine($"{Name} is working");
    }
}

class Faculty : Employee
{
    public string Subject { get; set; } = "";
}

Faculty faculty = new Faculty { Name = "Meera", Subject = "C#" };
faculty.Work();
Console.WriteLine(faculty.Subject);
```

**Output**

```text
Meera is working
C#
```

**Explanation:** `Faculty` inherits the public `Name` property and `Work()` method from `Employee`, and adds `Subject`. C# classes have one direct base class; a class can implement multiple interfaces.

## 5.7 Polymorphism

**Definition:** Polymorphism means that the same method call or base-type reference can produce behavior suited to different types.

**Why used:** It lets code work with a common type while each specific type supplies appropriate behavior. Overloading and overriding are two forms of polymorphism.

**Syntax and example: overriding**

```csharp
class Animal
{
    public virtual void Speak()
    {
        Console.WriteLine("Animal sound");
    }
}

class Dog : Animal
{
    public override void Speak()
    {
        Console.WriteLine("Woof");
    }
}

Animal pet = new Dog();
pet.Speak();
```

**Output:** `Woof`

**Explanation:** The variable is declared as `Animal`, but refers to a `Dog`. At runtime, C# calls the overridden `Dog.Speak()` method.

## 5.8 Method overloading

**Definition:** Overloading uses the same method name with different parameter lists in one type.

**Why used:** It gives one operation convenient forms for different inputs.

**Syntax and example**

```csharp
class Calculator
{
    public int Add(int a, int b) => a + b;
    public int Add(int a, int b, int c) => a + b + c;
}

Calculator calculator = new Calculator();
Console.WriteLine(calculator.Add(2, 3));
Console.WriteLine(calculator.Add(2, 3, 4));
```

**Output**

```text
5
9
```

**Explanation:** The argument count selects the matching overload. Overloads must differ by their parameters, not just their return type.

## 5.9 Method overriding

**Definition:** Overriding replaces an inherited `virtual` or `abstract` method implementation in a derived class.

**Why used:** A derived type can provide its own behavior while still being used through the base type.

**Syntax and example**

```csharp
class Employee
{
    public virtual void Describe()
    {
        Console.WriteLine("Employee");
    }
}

class Faculty : Employee
{
    public override void Describe()
    {
        Console.WriteLine("Faculty member");
    }
}

Employee person = new Faculty();
person.Describe();
```

**Output:** `Faculty member`

**Explanation:** `virtual` permits overriding. `override` supplies the derived implementation. The actual object type determines which implementation runs.

## 5.10 Abstraction and `abstract`

**Definition:** Abstraction shows what an object can do while hiding details of how it does it. An abstract class cannot be instantiated directly and may contain abstract members without a body.

**Why used:** It defines shared rules for related classes while requiring each concrete derived class to provide missing behavior.

**Syntax and example**

```csharp
abstract class Shape
{
    public abstract double GetArea();
}

class Rectangle : Shape
{
    public double Width { get; set; }
    public double Height { get; set; }

    public override double GetArea()
    {
        return Width * Height;
    }
}

Shape shape = new Rectangle { Width = 4, Height = 3 };
Console.WriteLine(shape.GetArea());
```

**Output:** `12`

**Explanation:** `Shape` says every concrete shape must provide `GetArea()`. `Rectangle` implements the calculation. `new Shape()` is not allowed because `Shape` is abstract.

## 5.11 Interface

**Definition:** An interface is a contract describing members a class agrees to implement.

**Why used:** Unrelated classes can provide the same capability, and code can use them through a shared interface.

**Syntax and example**

```csharp
interface IPrintable
{
    void Print();
}

class Report : IPrintable
{
    public void Print()
    {
        Console.WriteLine("Printing report");
    }
}

IPrintable item = new Report();
item.Print();
```

**Output:** `Printing report`

**Explanation:** `Report` implements `IPrintable` and supplies its public `Print()` method. The interface variable can refer to any object that implements that contract.

## 5.12 `virtual` and `override`

**Definition:** `virtual` marks a base-class member that derived classes may replace. `override` provides that replacement.

**Why used:** Together they enable runtime polymorphism for inherited methods.

**Syntax**

```csharp
class BaseType
{
    public virtual void Show() { /* base behavior */ }
}

class DerivedType : BaseType
{
    public override void Show() { /* specialized behavior */ }
}
```

**Example and output:** See [method overriding](#59-method-overriding); it prints `Faculty member` when the object is a `Faculty`.

**Explanation:** An `override` method must match an overridable base member. A method that is neither `virtual` nor `abstract` cannot be overridden this way.

## 5.13 Access modifiers

**Definition:** Access modifiers control which code can use a type or member.

**Why used:** They help protect internal details and make public APIs clear.

| Modifier | Basic meaning |
|---|---|
| `public` | Accessible to code that can access the containing type. |
| `private` | Accessible only within the containing type. This is the default for class members. |
| `protected` | Accessible within the containing type and its derived types. |
| `internal` | Accessible within the same assembly (compiled project). |

**Syntax and example**

```csharp
class Student
{
    public string Name { get; set; } = "";
    private int pin = 1234;
    protected string Course { get; set; } = "Computer Engineering";
    internal int BatchYear { get; set; } = 2026;

    public bool CheckPin(int enteredPin)
    {
        return pin == enteredPin;
    }
}

Student student = new Student { Name = "Asha" };
Console.WriteLine(student.CheckPin(1234));
```

**Output:** `True`

**Explanation:** `Name` is public and can be set from the example. `pin` cannot be read directly outside `Student`; `CheckPin()` provides a controlled check. `protected` is mainly useful to derived classes, and `internal` is useful within the same project/assembly.

## 5.14 Static members

**Definition:** A static field or method belongs to the class itself, not to an individual object.

**Why used:** Use static members for data or operations shared by all instances, or that do not need object-specific state.

**Syntax and example**

```csharp
class Student
{
    public static int StudentCount;

    public Student()
    {
        StudentCount++;
    }
}

Student first = new Student();
Student second = new Student();
Console.WriteLine(Student.StudentCount);
```

**Output:** `2`

**Explanation:** Each constructor call increments the one shared `StudentCount`. Access a static member using the class name: `Student.StudentCount`.

---

# Common Beginner Mistakes

| Mistake | How to fix it |
|---|---|
| Using the wrong letter case, such as `console.writeline` | C# is case-sensitive. Use `Console.WriteLine`. |
| Forgetting `;` after a statement | End most statements with a semicolon. Do not add one after a class or method body. |
| Using `=` to compare values | Use `==` for equality; `=` assigns a value. |
| Mixing types without conversion | Convert deliberately, such as `Convert.ToInt32(text)`, or use `TryParse()`. |
| Expecting integer division to keep decimals | Use `double` values, for example `10.0 / 3`. |
| Reading an array past its end | Valid indexes are `0` through `Length - 1`. Check the loop condition. |
| Forgetting to update a `while` loop condition | Change the counter or condition in the loop to avoid an infinite loop. |
| Calling an instance method using the class name | Create an object first, then call `objectName.MethodName()`. Static methods are called on the class. |
| Accessing a private field directly | Use a public property or method provided by the class. |
| Forgetting `break` in a traditional `switch` case | End each case with `break` when using this switch syntax. |
| Assuming `List<T>.Remove(value)` removes every duplicate | It removes the first matching item. Repeat carefully if all matches must be removed. |
| Assuming dictionary keys can repeat | Each dictionary key must be unique. Check with `ContainsKey()` or use the indexer to update a value. |
| Calling `Dequeue()`, `Pop()`, or `Peek()` on an empty collection | Check `Count` first. |
| Forgetting `f` or `m` on decimal literals | Use `1.5f` for `float` and `1.5m` for `decimal`. |
| Changing only a method's return type to overload it | Change the parameter list; return type alone does not distinguish overloads. |
| Overriding a method that is not `virtual` or `abstract` | Mark the base method `virtual` or `abstract`, then use `override`. |
| Declaring an abstract class object with `new` | Create an instance of a concrete derived class instead. |

---

# Important Differences

## `int` vs `double`

| `int` | `double` |
|---|---|
| Stores whole numbers. | Stores approximate decimal numbers. |
| Example: `int count = 5;` | Example: `double length = 5.25;` |
| Integer division drops the fractional part. | Division can produce a fractional value. |

## `float` vs `double` vs `decimal`

| Type | Precision/use | Literal suffix | Typical use |
|---|---|---|---|
| `float` | Lower precision, 32-bit | `f` | Situations where lower memory use is useful, such as some graphics data. |
| `double` | Higher precision than `float`, 64-bit | No suffix needed | General scientific and numeric calculations. |
| `decimal` | High base-10 precision | `m` | Money and decimal calculations where base-10 precision matters. |

> `float` and `double` use binary floating-point representation and may have small rounding differences. `decimal` is often preferred for money, but the application must still choose and apply its rounding rules.

## `class` vs `object`

| Class | Object |
|---|---|
| A blueprint/type definition. | An instance created from a class. |
| Describes members that objects can have. | Holds instance data and can use accessible members. |
| Example: `class Student { }` | Example: `Student s = new Student();` |

## Field vs property

| Field | Property |
|---|---|
| A variable declared in a type. | A member that provides controlled access to a value. |
| Often kept `private` for internal state. | Can validate or control reads and writes with `get` and `set`. |
| Example: `private int age;` | Example: `public int Age { get; set; }` |

## Method overloading vs method overriding

| Overloading | Overriding |
|---|---|
| Same method name, different parameter list. | Derived class replaces an inherited `virtual` or `abstract` method. |
| Usually declared in the same class. | Requires inheritance. |
| Selected from the argument list. | Selected at runtime based on the actual object type. |

## Abstract class vs interface

| Abstract class | Interface |
|---|---|
| Cannot be instantiated directly. | Cannot be instantiated directly. |
| Can contain fields, constructors, implemented methods, and abstract methods. | Defines a contract; classes provide its required implementation. |
| A class can have only one direct base class. | A class can implement multiple interfaces. |
| Use when related types share a base and possibly state/implementation. | Use to describe a capability or contract that different types can share. |

## Array vs `List<T>`

| Array | `List<T>` |
|---|---|
| Fixed length after creation. | Can grow or shrink. |
| Access items by index. | Access items by index and use collection methods. |
| Example: `int[] a = new int[3];` | Example: `List<int> a = new List<int>();` |

## `List<T>` vs `Dictionary<TKey, TValue>`

| `List<T>` | `Dictionary<TKey, TValue>` |
|---|---|
| Stores an ordered sequence; access by numeric index. | Stores key/value pairs; access by unique key. |
| Duplicates are allowed. | Keys must be unique; values can repeat. |
| Use when position/order in a sequence matters. | Use when you need to look up a value by a key. |

## `List<T>` vs `HashSet<T>`

| `List<T>` | `HashSet<T>` |
|---|---|
| Allows duplicate values and supports indexes. | Keeps values unique and has no index-based access. |
| Useful for ordered sequences. | Useful for uniqueness and membership checks. |
| `Add()` appends an item. | `Add()` adds an item only if it is not already present. |

---

# Practice Programs

Try to write each program yourself before looking up a solution. Use console input where useful, and test with more than one set of values.

## Concept 1: C# Basics

1. Read a student's name and age, then print a sentence containing both.
2. Read two integers and display their sum, difference, product, quotient, and remainder.
3. Convert a temperature in Celsius to Fahrenheit using `F = C * 9 / 5 + 32` and display the result.

## Concept 2: Control Statements

1. Read an integer and determine whether it is positive, negative, or zero.
2. Read marks and display a grade using an `if-else-if` chain of your choice.
3. Display the multiplication table of a number using a loop. Then modify the program to skip one chosen multiplier with `continue`.

## Concept 3: Methods

1. Write a method that accepts two integers and returns the larger one.
2. Write a method that accepts a radius and returns the area of a circle.
3. Overload a method named `Display` to print an integer, a string, and two integers. Add a method with an optional greeting parameter.

## Concept 4: Arrays and Collections

1. Store five marks in an array and calculate their total and average.
2. Use a `List<string>` to add names, search for a name, remove a name, and print the remaining names.
3. Use a `Dictionary<int, string>` for student IDs and names. Look up an ID safely with `TryGetValue()`; also create a `HashSet<int>` to find unique values.

## Concept 5: OOP

1. Create a `Student` class with roll number and name properties, a constructor, and a display method.
2. Create an `Employee` base class and a `Faculty` derived class. Override a method to display role-specific information.
3. Create an abstract `Shape` class with an abstract area method, then implement it in a `Rectangle` class. Add validation to a property using encapsulation.

---

# Viva Questions

1. **What is C#?** — A strongly typed programming language commonly used with .NET to build applications.
2. **What does `Main()` do?** — In the traditional program structure, it is the entry point where execution begins.
3. **What is a variable?** — A named storage location whose value can change.
4. **What is a constant?** — A value declared with `const` that cannot be changed in the program.
5. **What is the difference between `int` and `double`?** — `int` stores whole numbers; `double` stores approximate decimal numbers.
6. **What is type casting?** — Converting a value from one data type to another.
7. **What is the difference between `=` and `==`?** — `=` assigns a value; `==` compares two values.
8. **What is the difference between `while` and `do-while`?** — `while` checks before running; `do-while` checks after running and therefore runs at least once.
9. **What does `break` do?** — It exits the nearest loop or switch statement.
10. **What does `continue` do?** — It skips the rest of the current loop iteration.
11. **What is a method?** — A named block of code that performs a task.
12. **What is the difference between a parameter and an argument?** — A parameter is named in a method declaration; an argument is the value supplied in a call.
13. **What does `void` mean?** — The method does not return a value.
14. **What is method overloading?** — Using the same method name with different parameter lists.
15. **What is an array?** — A fixed-length group of values of the same type, accessed by indexes starting at zero.
16. **How is a `List<T>` different from an array?** — A list can change size; an array has a fixed length.
17. **What is a dictionary?** — A collection of unique keys mapped to values.
18. **What is a class and what is an object?** — A class is a blueprint; an object is an instance of that blueprint.
19. **What is encapsulation?** — Grouping data and behavior together and controlling access to internal data.
20. **What is the difference between an interface and an abstract class?** — An abstract class can share state and implementation with related types; an interface describes a contract that types can implement.

---

# Quick Revision

## Basic syntax

```csharp
int age = 20;
const double Pi = 3.14159;
string name = "Asha";
bool isReady = true;
Console.WriteLine($"{name} is {age}");
```

- `Console.ReadLine()` reads text; convert or parse it when a number is needed.
- `if`/`else` and `switch` choose a path.
- `for`, `while`, `do-while`, and `foreach` repeat work.
- `break` exits; `continue` skips to the next iteration.

## Method pattern

```csharp
static int Add(int first, int second)
{
    return first + second;
}
```

- Parameters are in the declaration; arguments are in the call.
- `void` means no return value.
- Overloads have different parameter lists.
- `ref` can change an already-initialized caller variable; `out` must be assigned in the method.

## Arrays and collections

```csharp
int[] values = { 10, 20, 30 };
List<int> list = new List<int>();
Dictionary<int, string> names = new Dictionary<int, string>();
HashSet<int> unique = new HashSet<int>();
Queue<string> queue = new Queue<string>();
Stack<string> stack = new Stack<string>();
```

- Array indexes start at `0`; arrays have fixed length.
- `List<T>` is resizable and allows duplicates.
- `Dictionary<TKey, TValue>` looks up values by unique key.
- `HashSet<T>` stores unique values.
- Queue: FIFO (`Enqueue`, `Dequeue`). Stack: LIFO (`Push`, `Pop`).

## OOP essentials

```csharp
class Student
{
    public string Name { get; set; } = "";
    public Student(string name) { Name = name; }
}

Student student = new Student("Asha");
```

- **Class:** blueprint. **Object:** instance.
- **Field:** class variable. **Property:** controlled access to a value.
- **Encapsulation:** protect data and expose safe operations.
- **Inheritance:** derived class reuses base class members.
- **Polymorphism:** a common call can behave differently for different types.
- **Abstraction:** show essential behavior and hide implementation detail.
- `virtual` allows overriding; `override` supplies derived behavior; `abstract` requires derived implementation.
- `public`, `private`, `protected`, and `internal` control accessibility.
- `static` members belong to the type, not an individual instance.

**Remember:** Write clear names, match types correctly, check loop and array boundaries, and compile/run small examples often.
