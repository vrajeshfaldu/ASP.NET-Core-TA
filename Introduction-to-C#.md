
# Lab 02 | Class, Objects, Data Members & Member Functions in C#

## 1. Objective

The objective of this lab is to understand the basic concepts of Object-Oriented Programming (OOP) in C#.

After completing this lab, students will be able to:

- Understand classes and objects.
- Declare data members inside a class.
- Create member functions.
- Create objects using the `new` keyword.
- Access class functionality through objects.
- Accept and display user input.
- Understand the basic structure of a C# program.

---

## 2. Why Are We Doing This Lab?

### What?

In this lab, we learn how to create classes and objects in C# and how to organize related data and functions inside a class.

### Why?

In real-world applications, we need to store and manage different types of information.

For example:
- A student has a name, roll number and percentage.
- An employee has an ID, name and salary.
- A faculty member has an ID, name, age, weight and height.

Instead of writing separate variables and functions everywhere, we can group related information inside a class.

### When?

Classes and objects are useful when developing:

- Student Management Systems
- Employee Management Systems
- Hospital Management Systems
- Banking Applications
- Library Management Systems

### How?

We can implement these concepts by:

1. Creating a class.
2. Declaring data members.
3. Defining member functions.
4. Creating an object of the class.
5. Calling member functions using the object.

---

## 3. Real-Life Example of Class and Object

Suppose we want to store information about faculty members in a college.

Every faculty member has:

- ID
- Name
- Age
- Weight
- Height

We can create a class named `Faculty` containing these data members and functions to accept and display faculty details.

The class acts as a blueprint, and individual faculty objects represent actual faculty members.

### Understanding the Relationship

**Class: Faculty**

Data Members:
- ID
- Name
- Age
- Weight
- Height

Member Functions:
- `GetFacultyDetails()`
- `DisplayFacultyDetails()`

**Objects:**
- `faculty1`
- `faculty2`
- `faculty3`

Each object can store information about a different faculty member.

---

## 4. Concept Overview

The following concepts are important in this lab:

1. Class
2. Object
3. Data Members
4. Member Functions
5. Access Modifiers
6. `new` Keyword
7. `this` Keyword
8. Constructor
9. Array of Objects

---

# 5. Detailed Theory

## 5.1 Class

A class is a user-defined data type that groups related data members and member functions together.

In simple words, a class is a blueprint used to create objects.

### Syntax

```csharp
class ClassName
{
    // Data members

    // Member functions
}
```

### Example

```csharp
class Student
{
    int id;
    string name;

    public void Display()
    {
        Console.WriteLine(id);
        Console.WriteLine(name);
    }
}
```

Here:
- `Student` is the class name.
- `id` and `name` are data members.
- `Display()` is a member function.

**Important:** A class defines the structure and behavior of its objects.

## 5.2 Object

An object is an instance of a class.

In simple words, an object is created from a class and is used to access its members.

### Syntax

```csharp
ClassName objectName = new ClassName();
```

### Example

```csharp
Student s1 = new Student();
```

Explanation:

- `Student` is the class name.
- `s1` is the object name.
- `new` is used to create an object.
- `Student()` calls the constructor.

Here, `s1` is an object of the `Student` class.

**Remember:** A class is a blueprint, whereas an object is an instance of that blueprint.

## 5.3 Data Members

Data members are variables declared inside a class.

They are used to store information related to an object.

### Example

```csharp
class Employee
{
    int id;
    string name;
    double salary;
}
```

Here:

- `id` stores employee ID.
- `name` stores employee name.
- `salary` stores employee salary.

These variables are called data members.

## 5.4 Member Functions

Member functions are methods declared inside a class.

They define the operations that an object can perform.

### Example

```csharp
class Student
{
    int id;

    public void Display()
    {
        Console.WriteLine(id);
    }
}
```

Here, `Display()` is a member function.

It is used to display the value of `id`.

## 5.5 Access Modifiers

Access modifiers define the accessibility of classes and their members.

Important access modifiers in C#:

| Access Modifier | Meaning |
|---|---|
| `public` | Accessible from anywhere allowed by the containing type |
| `private` | Accessible only inside the containing type |
| `protected` | Accessible inside the class and its derived classes |
| `internal` | Accessible within the same assembly |

### Example

```csharp
class Student
{
    private int id;

    public void Display()
    {
        Console.WriteLine(id);
    }
}
```

Here:
- `id` is private.
- `Display()` is public.

**Important:** If no access modifier is specified for a class member, its default accessibility is generally private.

## 5.6 `new` Keyword

The `new` keyword is used to create an instance of a class.

### Example

```csharp
Student s1 = new Student();
```

Here, `new Student()` creates an instance of the Student class.

We can then use the object to access its accessible members.

## 5.7 `this` Keyword

The `this` keyword refers to the current instance of a class.

It is commonly used when a constructor parameter and a class variable have the same name.

### Example

```csharp
class Student
{
    int id;

    public Student(int id)
    {
        this.id = id;
    }
}
```

Explanation:

- `this.id` refers to the class-level variable.
- `id` refers to the constructor parameter.

**Remember:** `this` represents the current object.

## 5.8 Constructor

A constructor is a special member of a class that is called automatically when an object is created.

It is mainly used to initialize objects.

### Characteristics of a Constructor

1. Its name must be the same as the class name.
2. It does not have a return type.
3. It is called automatically when an object is created.
4. It can accept parameters.
5. It can be overloaded.

### Example

```csharp
class Student
{
    int id;

    public Student(int id)
    {
        this.id = id;
    }
}
```

Creating an object:

```csharp
Student s1 = new Student(101);
```

Here, the constructor receives `101` and assigns it to the object's `id` variable.

## 5.9 Array of Objects

An array of objects is used to store multiple objects of the same class.

Instead of creating separate variables for every object, we can use an array.

### Example

```csharp
Student[] students = new Student[3];

for (int i = 0; i < 3; i++)
{
    students[i] = new Student();
}
```

Explanation:

- `Student[]` declares an array of Student references.
- `new Student[3]` creates an array that can hold three references.
- `students[i] = new Student()` creates an individual Student object at each position.

**Important:** Creating the array does not automatically create all the Student objects. Each object must be initialized separately.

---

# 6. Practical Implementation

## Program 1: Accept and Display Faculty Details

### Problem Statement

Write a C# program to create a `Faculty` class containing the following data members:

- Faculty ID
- Faculty Name
- Faculty Age
- Faculty Weight
- Faculty Height

Create two member functions:

1. `GetFacultyDetails()` – Accept faculty details from the user.
2. `DisplayFacultyDetails()` – Display the entered faculty details.

### Source Code: Faculty.cs

```csharp
using System;

namespace Lab2
{
    internal class Faculty
    {
        int id;
        string name;
        int age;
        double weight;
        double height;

        public void GetFacultyDetails()
        {
            Console.WriteLine("Enter faculty id: ");
            id = Convert.ToInt32(Console.ReadLine());

            Console.WriteLine("Enter faculty name: ");
            name = Console.ReadLine();

            Console.WriteLine("Enter faculty age: ");
            age = Convert.ToInt32(Console.ReadLine());

            Console.WriteLine("Enter faculty weight: ");
            weight = Convert.ToDouble(Console.ReadLine());

            Console.WriteLine("Enter faculty height: ");
            height = Convert.ToDouble(Console.ReadLine());
        }

        public void DisplayFacultyDetails()
        {
            Console.WriteLine("\nFaculty Details: ");
            Console.WriteLine("Id: " + id);
            Console.WriteLine("Name: " + name);
            Console.WriteLine("Age: " + age);
            Console.WriteLine("Weight: " + weight);
            Console.WriteLine("Height: " + height);
        }
    }
}
```

### Main Program: Program.cs

To execute the Faculty class, we need to create its object inside the `Main()` method.

```csharp
using System;

namespace Lab2
{
    internal class Program
    {
        static void Main(string[] args)
        {
            Faculty faculty = new Faculty();

            faculty.GetFacultyDetails();

            faculty.DisplayFacultyDetails();
        }
    }
}
```

---

# 7. Detailed Code Explanation

Let's understand the program step by step.

### Step 1: Import System Namespace

```csharp
using System;
```

- `System` is a namespace in C#.
- It provides commonly used classes.
- We use `Console` to accept and display information.

### Step 2: Create a Namespace

```csharp
namespace Lab2
{
}
```

- A namespace is used to organize related classes.
- Here, `Lab2` is the namespace name.
- Both `Program` and `Faculty` belong to the same namespace.

### Step 3: Create a Class

```csharp
internal class Faculty
{
}
```

- `class` is a keyword used to declare a class.
- `Faculty` is the class name.
- `internal` means the class can be accessed within the same assembly.

### Step 4: Declare Data Members

```csharp
int id;
string name;
int age;
double weight;
double height;
```

These variables store faculty information.

| Variable | Data Type | Purpose |
|---|---|---|
| `id` | `int` | Stores faculty ID |
| `name` | `string` | Stores faculty name |
| `age` | `int` | Stores faculty age |
| `weight` | `double` | Stores faculty weight |
| `height` | `double` | Stores faculty height |

**Why use double for weight and height?**

Because weight and height may contain decimal values.

For example:
- Weight = 55.5
- Height = 165.5

### Step 5: Create `GetFacultyDetails()` Function

```csharp
public void GetFacultyDetails()
{
    Console.WriteLine("Enter faculty id: ");
    id = Convert.ToInt32(Console.ReadLine());
}
```

- `public` makes the method accessible from outside the class, subject to the containing class's accessibility.
- `void` means the method does not return a value.
- `GetFacultyDetails()` is the method name.
- `Console.WriteLine()` displays a message.
- `Console.ReadLine()` accepts input as a string.
- `Convert.ToInt32()` converts the input into an integer.

The same process is used to accept the remaining faculty details.

### Step 6: Create `DisplayFacultyDetails()` Function

```csharp
public void DisplayFacultyDetails()
{
    Console.WriteLine("\nFaculty Details: ");
    Console.WriteLine("Id: " + id);
    Console.WriteLine("Name: " + name);
    Console.WriteLine("Age: " + age);
    Console.WriteLine("Weight: " + weight);
    Console.WriteLine("Height: " + height);
}
```

This method displays the values stored in the data members.

Here, `+` is used to join the text with the variable values.

### Step 7: Create an Object

```csharp
Faculty faculty = new Faculty();
```

This statement creates an object of the Faculty class.

Let's understand each part:

| Part | Meaning |
|---|---|
| `Faculty` | Class name |
| `faculty` | Object name |
| `new` | Keyword used to create an instance |
| `Faculty()` | Constructor call |

### Step 8: Call Member Functions

```csharp
faculty.GetFacultyDetails();

faculty.DisplayFacultyDetails();
```

The dot (`.`) operator is used to access accessible members through an object.

First, the program accepts faculty details.

Then, it displays the entered details.

---

# 8. Program Execution Flow

The execution of the program takes place in the following order:

1. Program execution starts from `Main()`.
2. A Faculty object is created.
3. `GetFacultyDetails()` is called.
4. The user enters faculty information.
5. The values are stored in the object's data members.
6. `DisplayFacultyDetails()` is called.
7. The faculty information is displayed.

---

# 9. Expected Output

```text
Enter faculty id:
101

Enter faculty name:
Rahul Patel

Enter faculty age:
35

Enter faculty weight:
65.5

Enter faculty height:
170.5

Faculty Details:
Id: 101
Name: Rahul Patel
Age: 35
Weight: 65.5
Height: 170.5
```

*Note: The above is a sample output using example input values.*

---

# 10. Common Mistakes and Their Solutions

Students commonly make the following mistakes while working with classes and objects.

## Mistake 1: Forgetting to Create an Object

Incorrect:

```csharp
Faculty.GetFacultyDetails();
```

Why is it incorrect?

`GetFacultyDetails()` is an instance method. It cannot be called directly using the class name.

Correct:

```csharp
Faculty faculty = new Faculty();

faculty.GetFacultyDetails();
```

## Mistake 2: Forgetting the `new` Keyword

Incorrect:

```csharp
Faculty faculty;
faculty.GetFacultyDetails();
```

Why is it incorrect?

The variable is declared, but an object has not been created.

Correct:

```csharp
Faculty faculty = new Faculty();
faculty.GetFacultyDetails();
```

## Mistake 3: Using the Wrong Data Type

Incorrect:

```csharp
int weight;
```

Why is it incorrect?

An integer cannot store decimal values.

Correct:

```csharp
double weight;
```

## Mistake 4: Forgetting Type Conversion

Incorrect:

```csharp
id = Console.ReadLine();
```

Why is it incorrect?

`Console.ReadLine()` returns a string, whereas `id` is an integer.

Correct:

```csharp
id = Convert.ToInt32(Console.ReadLine());
```

## Mistake 5: Confusing Class and Object

Incorrect understanding:

Class and object are the same.

Correct understanding:

- Class is a blueprint.
- Object is an instance of a class.

## Mistake 6: Calling the Wrong Method Name

Incorrect:

```csharp
faculty.GetFacultyDetail();
```

Correct:

```csharp
faculty.GetFacultyDetails();
```

The method name must match its declaration.

## Mistake 7: Accessing Private Members Directly

Incorrect:

```csharp
faculty.id = 101;
```

Why is it incorrect?

The `id` member is private by default.

It cannot be accessed directly outside the Faculty class.

Use a suitable public method to work with private data.

---

# 11. Important Points to Remember

These points are important for practical exams, viva and interviews.

1. A class is a blueprint for creating objects.
2. An object is an instance of a class.
3. Data members store information.
4. Member functions define the behavior of a class.
5. The `new` keyword is used to create an object.
6. The dot (`.`) operator is used to access accessible members through an object.
7. `Console.ReadLine()` returns a string.
8. `Convert.ToInt32()` converts a value into an integer.
9. `Convert.ToDouble()` converts a value into a double.
10. `void` indicates that a method does not return a value.
11. Private members cannot be accessed directly outside their containing class.
12. A constructor has the same name as its class.
13. A constructor does not have a return type.
14. A constructor is called when an object is created.
15. An array of objects can store multiple objects of the same class.
16. Every object in an array must be initialized before its instance members are used.

---

# 12. Difference Between Class and Object

| Class | Object |
|---|---|
| Class is a blueprint. | Object is an instance of a class. |
| It defines data members and methods. | It uses the class-defined members. |
| It is declared using the `class` keyword. | It is commonly created using `new`. |
| Example: `Faculty` | Example: `faculty1` |

---

# 13. Important Viva Questions and Answers

### Q1. What is a class?

A class is a user-defined data type that groups related data members and member functions together.

### Q2. What is an object?

An object is an instance of a class.

### Q3. What is the difference between class and object?

A class is a blueprint, whereas an object is an instance created from that blueprint.

### Q4. What are data members?

Data members are variables declared inside a class to store information.

### Q5. What are member functions?

Member functions are methods declared inside a class to perform operations.

### Q6. What is the use of the `new` keyword?

The `new` keyword is used to create an instance of a class.

### Q7. What is a constructor?

A constructor is a special member of a class that is called automatically when an object is created.

### Q8. Does a constructor have a return type?

No. A constructor does not have a return type, not even `void`.

### Q9. What is the use of the `this` keyword?

The `this` keyword refers to the current instance of a class.

### Q10. What is an array of objects?

An array of objects is used to store multiple objects of the same class.

### Q11. What is the use of `void`?

`void` specifies that a method does not return a value.

### Q12. What is the difference between `ReadLine()` and `WriteLine()`?

- `ReadLine()` accepts input from the user.
- `WriteLine()` displays output on the console.

### Q13. Why do we use `Convert.ToInt32()`?

It converts the input string into an integer.

### Q14. Can we create multiple objects of one class?

Yes. We can create multiple objects of the same class.

### Q15. What is the default accessibility of class members?

Class members have private accessibility by default when no access modifier is specified.

---

# 14. Practice Questions for Students

Try solving these programs independently.

### Basic Level

1. Create a Student class with Roll Number, Name and Percentage. Accept and display student details.
2. Create an Employee class with ID, Name and Salary. Display employee information.
3. Create a Book class with Book ID, Title and Price.

### Intermediate Level

4. Create a class named Product with Product ID, Product Name and Quantity. Accept and display product information.
5. Create a class named Faculty and store information about three faculty members using an array of objects.
6. Create a class named Rectangle and calculate its area using a member function.

### Constructor-Based Questions

7. Create a Student class and initialize its values using a parameterized constructor.
8. Create a Car class and initialize its Make, Model and Year using a constructor.
9. Create a Cube class and calculate its volume using a parameterized constructor.

---

# 15. Quick Revision

Before completing this lab, make sure you understand these concepts:

- [ ] What is a class?
- [ ] What is an object?
- [ ] What are data members?
- [ ] What are member functions?
- [ ] How do we create an object?
- [ ] What is the purpose of the `new` keyword?
- [ ] What is a constructor?
- [ ] What is the `this` keyword?
- [ ] What is an array of objects?
- [ ] How do we accept and display user input?

---

# 16. Conclusion

In this lab, we learned the basic concepts of Object-Oriented Programming in C#.

We understood how to create a class, declare data members, define member functions and create objects.

We also learned how to accept user input and display information using class methods.

These concepts are the foundation of Object-Oriented Programming and are useful for developing real-world applications such as Student Management Systems, Employee Management Systems and Hospital Management Systems.

**Happy Learning!**
