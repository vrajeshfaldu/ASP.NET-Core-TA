# Lab 2 – Class, Objects, Array of Objects & Constructor

**Course:** 2401CS521 – Building RESTful APIs with ASP.NET Core  
**University:** Darshan University  
**Lab:** 2  
**Topic:** Class, Objects, Array of Objects & Constructor

> **Note:** The Lab 2 manual contains Questions 1–6 under the topic **Class, Objects, Array of Objects & Constructor**. Questions 1–4 are **Part A**, while Questions 5–6 are **Part B**.

---

# 1. Learning Objectives

After completing this lab, students should be able to:

- Create a class in C#.
- Create objects from a class.
- Declare fields/data members.
- Create and call methods.
- Create constructors.
- Use parameterized constructors.
- Create multiple objects.
- Store objects in an array.
- Accept and display object data.
- Understand the difference between a class, object, constructor, and array of objects.

---

# 2. Basic Theory

## 2.1 Class

A **class** is a blueprint/template used to create objects.

### Syntax

```csharp
class Student
{
    public int Id;
    public string Name;
}
```

The class defines what data and behavior an object will have.

---

## 2.2 Object

An **object** is an instance of a class.

```csharp
Student s1 = new Student();
```

Here:

- `Student` → class
- `s1` → object/reference variable
- `new Student()` → creates an object

---

## 2.3 Data Members

Data members store information about an object.

```csharp
class Faculty
{
    public int ID;
    public string Name;
    public int Age;
}
```

Here `ID`, `Name`, and `Age` are data members.

---

## 2.4 Member Function / Method

A method defines an operation that an object can perform.

```csharp
public void Display()
{
    Console.WriteLine(Name);
}
```

---

## 2.5 Constructor

A constructor is a special method that is automatically called when an object is created.

Important points:

- Constructor has the same name as the class.
- It has no return type.
- It is called automatically when `new` is used.
- It is commonly used to initialize object data.

### Example

```csharp
class Student
{
    public int Id;
    public string Name;

    public Student(int id, string name)
    {
        Id = id;
        Name = name;
    }
}
```

Creating the object:

```csharp
Student s = new Student(1, "Rahul");
```

---

## 2.6 Array of Objects

An array of objects stores multiple objects of the same class.

```csharp
Student[] students = new Student[5];

students[0] = new Student(1, "Rahul");
students[1] = new Student(2, "Amit");
```

The array contains references to `Student` objects.

---

# Part A

---

# Question 1

## Question

**Write a program to create a class named `Faculty` with `ID`, `Name`, `Age`, `Weight` and `Height` as data members and also create member functions like `GetFacultyDetails()` and `DisplayFacultyDetails()`.**

### Concepts Used

- Class
- Object
- Data members
- Methods
- Console input
- Console output

## Code

```csharp
using System;

class Faculty
{
    public int ID;
    public string Name;
    public int Age;
    public double Weight;
    public double Height;

    public void GetFacultyDetails()
    {
        Console.Write("Enter Faculty ID: ");
        ID = Convert.ToInt32(Console.ReadLine());

        Console.Write("Enter Faculty Name: ");
        Name = Console.ReadLine();

        Console.Write("Enter Age: ");
        Age = Convert.ToInt32(Console.ReadLine());

        Console.Write("Enter Weight: ");
        Weight = Convert.ToDouble(Console.ReadLine());

        Console.Write("Enter Height: ");
        Height = Convert.ToDouble(Console.ReadLine());
    }

    public void DisplayFacultyDetails()
    {
        Console.WriteLine("\n--- Faculty Details ---");
        Console.WriteLine($"ID     : {ID}");
        Console.WriteLine($"Name   : {Name}");
        Console.WriteLine($"Age    : {Age}");
        Console.WriteLine($"Weight : {Weight}");
        Console.WriteLine($"Height : {Height}");
    }
}

class Program
{
    static void Main()
    {
        Faculty faculty = new Faculty();

        faculty.GetFacultyDetails();
        faculty.DisplayFacultyDetails();
    }
}
```

## Explanation

### 1. Create the class

```csharp
class Faculty
```

Creates a class named `Faculty`.

### 2. Data members

```csharp
public int ID;
public string Name;
public int Age;
public double Weight;
public double Height;
```

These variables store faculty information.

### 3. GetFacultyDetails()

```csharp
public void GetFacultyDetails()
```

This method accepts data from the user.

### 4. DisplayFacultyDetails()

```csharp
public void DisplayFacultyDetails()
```

This method displays the stored faculty information.

### 5. Create object

```csharp
Faculty faculty = new Faculty();
```

This creates an object of the `Faculty` class.

### Expected Output

```text
Enter Faculty ID: 101
Enter Faculty Name: Rajesh
Enter Age: 35
Enter Weight: 72
Enter Height: 5.8

--- Faculty Details ---
ID     : 101
Name   : Rajesh
Age    : 35
Weight : 72
Height : 5.8
```

---

# Question 2

## Question

**Write a program to create a class named `Employee` with `Emp_ID`, `Name`, `Department`, `Designation` and `Salary` as data members and member functions like `GetEmpDetails()` and `DisplayEmpDetails()` for five different employee objects.**

### Concepts Used

- Class
- Object
- Methods
- Multiple objects
- Array of objects
- Loop

## Code

```csharp
using System;

class Employee
{
    public int Emp_ID;
    public string Name;
    public string Department;
    public string Designation;
    public double Salary;

    public void GetEmpDetails()
    {
        Console.Write("Enter Employee ID: ");
        Emp_ID = Convert.ToInt32(Console.ReadLine());

        Console.Write("Enter Name: ");
        Name = Console.ReadLine();

        Console.Write("Enter Department: ");
        Department = Console.ReadLine();

        Console.Write("Enter Designation: ");
        Designation = Console.ReadLine();

        Console.Write("Enter Salary: ");
        Salary = Convert.ToDouble(Console.ReadLine());
    }

    public void DisplayEmpDetails()
    {
        Console.WriteLine(
            $"{Emp_ID,-10} {Name,-15} {Department,-15} {Designation,-15} {Salary}"
        );
    }
}

class Program
{
    static void Main()
    {
        Employee[] employees = new Employee[5];

        for (int i = 0; i < 5; i++)
        {
            Console.WriteLine($"\nEnter details for Employee {i + 1}");

            employees[i] = new Employee();
            employees[i].GetEmpDetails();
        }

        Console.WriteLine("\n--- Employee Details ---");
        Console.WriteLine(
            $"{"ID",-10} {"Name",-15} {"Department",-15} {"Designation",-15} Salary"
        );

        for (int i = 0; i < 5; i++)
        {
            employees[i].DisplayEmpDetails();
        }
    }
}
```

## Explanation

### Array of objects

```csharp
Employee[] employees = new Employee[5];
```

This creates an array capable of storing references to five `Employee` objects.

### Creating each object

```csharp
employees[i] = new Employee();
```

Creates an `Employee` object and stores its reference at position `i`.

### Why use a loop?

Instead of writing five separate blocks of code, a loop lets us process all five employees.

### Expected Output

```text
--- Employee Details ---
ID         Name            Department      Designation      Salary
101        Rahul           IT              Developer        30000
102        Amit            HR              Manager          45000
103        Priya           Finance         Accountant       35000
104        Neha            IT              Tester           32000
105        Jay             Sales           Executive        28000
```

---

# Question 3

## Question

**Write a program to calculate the Volume of a Cube using constructor.**

### Formula

```text
Volume of Cube = Side × Side × Side
```

or

```text
Volume = Side³
```

### Concepts Used

- Class
- Constructor
- Object
- Calculation

## Code

```csharp
using System;

class Cube
{
    public double Side;
    public double Volume;

    public Cube(double side)
    {
        Side = side;
        Volume = Side * Side * Side;
    }

    public void DisplayVolume()
    {
        Console.WriteLine($"Side = {Side}");
        Console.WriteLine($"Volume of Cube = {Volume}");
    }
}

class Program
{
    static void Main()
    {
        Console.Write("Enter side of cube: ");
        double side = Convert.ToDouble(Console.ReadLine());

        Cube cube = new Cube(side);

        cube.DisplayVolume();
    }
}
```

## Explanation

### Constructor

```csharp
public Cube(double side)
```

This is a parameterized constructor.

It receives the side value when the object is created.

```csharp
Cube cube = new Cube(side);
```

The constructor is automatically called here.

### Calculation

```csharp
Volume = Side * Side * Side;
```

Calculates the volume of the cube.

### Expected Output

```text
Enter side of cube: 5
Side = 5
Volume of Cube = 125
```

---

# Question 4

## Question

**A car dealership wants to display details of premium sports cars. Create a `Car` object with `Make`, `Model`, `Year`, `FuelType` and `Horsepower` details using a constructor.**

### Concepts Used

- Class
- Object
- Constructor
- Constructor parameters
- Data members

## Code

```csharp
using System;

class Car
{
    public string Make;
    public string Model;
    public int Year;
    public string FuelType;
    public int Horsepower;

    public Car(
        string make,
        string model,
        int year,
        string fuelType,
        int horsepower)
    {
        Make = make;
        Model = model;
        Year = year;
        FuelType = fuelType;
        Horsepower = horsepower;
    }

    public void DisplayCarDetails()
    {
        Console.WriteLine("\n--- Car Details ---");
        Console.WriteLine($"Make       : {Make}");
        Console.WriteLine($"Model      : {Model}");
        Console.WriteLine($"Year       : {Year}");
        Console.WriteLine($"Fuel Type  : {FuelType}");
        Console.WriteLine($"Horsepower : {Horsepower} HP");
    }
}

class Program
{
    static void Main()
    {
        Car car = new Car(
            "BMW",
            "M4",
            2026,
            "Petrol",
            503
        );

        car.DisplayCarDetails();
    }
}
```

## Explanation

The constructor accepts five values:

```csharp
Car(
    string make,
    string model,
    int year,
    string fuelType,
    int horsepower)
```

When this statement executes:

```csharp
Car car = new Car("BMW", "M4", 2026, "Petrol", 503);
```

the constructor initializes all five data members.

### Expected Output

```text
--- Car Details ---
Make       : BMW
Model      : M4
Year       : 2026
Fuel Type  : Petrol
Horsepower : 503 HP
```

---

# Part B

---

# Question 5

## Question

**Write a program to manage a supermarket inventory. Create an `Item` class with `Item_Code`, `Item_Name`, and `Stock_Quantity`. Use a constructor to initialize the objects and an array of objects to store and display the details of 10 different items.**

### Concepts Used

- Class
- Object
- Parameterized constructor
- Array of objects
- Loop

## Code

```csharp
using System;

class Item
{
    public int Item_Code;
    public string Item_Name;
    public int Stock_Quantity;

    public Item(int itemCode, string itemName, int stockQuantity)
    {
        Item_Code = itemCode;
        Item_Name = itemName;
        Stock_Quantity = stockQuantity;
    }

    public void DisplayItem()
    {
        Console.WriteLine(
            $"{Item_Code,-12} {Item_Name,-20} {Stock_Quantity}"
        );
    }
}

class Program
{
    static void Main()
    {
        Item[] items = new Item[10];

        for (int i = 0; i < 10; i++)
        {
            Console.WriteLine($"\nEnter details for Item {i + 1}");

            Console.Write("Item Code: ");
            int code = Convert.ToInt32(Console.ReadLine());

            Console.Write("Item Name: ");
            string name = Console.ReadLine();

            Console.Write("Stock Quantity: ");
            int quantity = Convert.ToInt32(Console.ReadLine());

            items[i] = new Item(code, name, quantity);
        }

        Console.WriteLine("\n--- Supermarket Inventory ---");
        Console.WriteLine(
            $"{"Item Code",-12} {"Item Name",-20} Stock Quantity"
        );

        for (int i = 0; i < 10; i++)
        {
            items[i].DisplayItem();
        }
    }
}
```

## Explanation

### Step 1: Create Item class

```csharp
class Item
```

The class represents one inventory item.

### Step 2: Constructor

```csharp
public Item(int itemCode, string itemName, int stockQuantity)
```

The constructor initializes an item.

### Step 3: Create array

```csharp
Item[] items = new Item[10];
```

This creates an array that can store ten `Item` object references.

### Step 4: Create objects

```csharp
items[i] = new Item(code, name, quantity);
```

Each array position receives an `Item` object.

### Expected Output

```text
--- Supermarket Inventory ---
Item Code    Item Name            Stock Quantity
101          Rice                 50
102          Sugar                40
103          Oil                  30
...
110          Biscuits             25
```

---

# Question 6

## Question

**A clinic management system needs to store patient records. Create a program with a `Patient` class containing `Patient_ID`, `Name`, `Age`, and `Disease`. Use an array of objects to accept details for multiple patients and display a formatted summary report of all patients.**

### Concepts Used

- Class
- Object
- Array of objects
- Constructor
- Methods
- Loop
- Formatted output

## Code

```csharp
using System;

class Patient
{
    public int Patient_ID;
    public string Name;
    public int Age;
    public string Disease;

    public Patient(
        int patientId,
        string name,
        int age,
        string disease)
    {
        Patient_ID = patientId;
        Name = name;
        Age = age;
        Disease = disease;
    }

    public void DisplayPatient()
    {
        Console.WriteLine(
            $"{Patient_ID,-12} {Name,-20} {Age,-8} {Disease}"
        );
    }
}

class Program
{
    static void Main()
    {
        Console.Write("Enter number of patients: ");
        int n = Convert.ToInt32(Console.ReadLine());

        Patient[] patients = new Patient[n];

        for (int i = 0; i < n; i++)
        {
            Console.WriteLine($"\nEnter details for Patient {i + 1}");

            Console.Write("Patient ID: ");
            int id = Convert.ToInt32(Console.ReadLine());

            Console.Write("Name: ");
            string name = Console.ReadLine();

            Console.Write("Age: ");
            int age = Convert.ToInt32(Console.ReadLine());

            Console.Write("Disease: ");
            string disease = Console.ReadLine();

            patients[i] = new Patient(id, name, age, disease);
        }

        Console.WriteLine("\n========== Patient Summary Report ==========");
        Console.WriteLine(
            $"{"Patient ID",-12} {"Name",-20} {"Age",-8} Disease"
        );

        for (int i = 0; i < n; i++)
        {
            patients[i].DisplayPatient();
        }
    }
}
```

## Explanation

### Dynamic array size

```csharp
int n = Convert.ToInt32(Console.ReadLine());
Patient[] patients = new Patient[n];
```

The user decides how many patient records are required.

### Constructor

```csharp
patients[i] = new Patient(id, name, age, disease);
```

The constructor initializes each patient's data.

### Display report

```csharp
patients[i].DisplayPatient();
```

Calls the display method for every patient.

### Expected Output

```text
Enter number of patients: 3

========== Patient Summary Report ==========
Patient ID   Name                 Age      Disease
101          Rahul                25       Fever
102          Priya                31       Migraine
103          Amit                 42       Diabetes
```

---

# 3. Important Differences

## Class vs Object

| Class | Object |
|---|---|
| Blueprint/template | Instance of a class |
| Does not represent a specific entity | Represents a specific entity |
| Example: `Student` | Example: `Student s1` |

---

## Constructor vs Method

| Constructor | Method |
|---|---|
| Initializes an object | Performs an operation |
| Same name as class | Can have any valid name |
| Has no return type | May return a value |
| Called automatically during object creation | Called explicitly |
| Used mainly for initialization | Used for behavior/functionality |

---

## Single Object vs Array of Objects

### Single object

```csharp
Student s1 = new Student();
```

Stores one object.

### Array of objects

```csharp
Student[] students = new Student[5];
```

Stores multiple object references.

---

# 4. Common Beginner Mistakes

### Mistake 1: Forgetting `new`

Incorrect:

```csharp
Student s;
s.Name = "Rahul";
```

Correct:

```csharp
Student s = new Student();
s.Name = "Rahul";
```

---

### Mistake 2: Creating an array but not creating objects

```csharp
Student[] students = new Student[5];
```

This creates the array, but it does **not** create five Student objects.

You still need:

```csharp
students[0] = new Student();
```

---

### Mistake 3: Constructor name does not match class name

Incorrect:

```csharp
class Student
{
    public Employee()
    {
    }
}
```

Correct:

```csharp
class Student
{
    public Student()
    {
    }
}
```

---

### Mistake 4: Giving a return type to a constructor

Incorrect:

```csharp
public void Student()
{
}
```

Correct:

```csharp
public Student()
{
}
```

---

# 5. Viva Questions

### Q1. What is a class?

A class is a blueprint or template for creating objects.

### Q2. What is an object?

An object is an instance of a class.

### Q3. What is a constructor?

A constructor is a special member that is automatically called when an object is created.

### Q4. Does a constructor have a return type?

No.

### Q5. Can a constructor have parameters?

Yes. It is called a parameterized constructor.

### Q6. What is an array of objects?

It is an array used to store references to multiple objects of the same class.

### Q7. Why do we use constructors?

Constructors are commonly used to initialize object data.

### Q8. When is a constructor called?

It is called automatically when an object is created using `new`.

### Q9. Can a class contain methods?

Yes.

### Q10. Can we create multiple objects from one class?

Yes.

### Q11. What does `new` do?

`new` creates an object and allocates the required memory.

### Q12. Why is a loop useful with an array of objects?

It allows us to process multiple objects without repeating code.

### Q13. What is a data member?

A variable declared inside a class that stores object/class data.

### Q14. What is a member function?

A method declared inside a class that defines behavior.

### Q15. What is the difference between `Student s = new Student()` and `Student[] s = new Student[5]`?

The first creates one object. The second creates an array capable of holding five `Student` object references.

---

# 6. Quick Revision

```text
Class
  ↓
Blueprint

Object
  ↓
Instance of Class

Constructor
  ↓
Initializes Object

Array of Objects
  ↓
Stores Multiple Objects
```

### Basic Class

```csharp
class Student
{
    public int Id;
    public string Name;
}
```

### Object

```csharp
Student s = new Student();
```

### Constructor

```csharp
public Student(int id, string name)
{
    Id = id;
    Name = name;
}
```

### Object using Constructor

```csharp
Student s = new Student(1, "Rahul");
```

### Array of Objects

```csharp
Student[] students = new Student[5];

for (int i = 0; i < 5; i++)
{
    students[i] = new Student();
}
```

---

# 7. Lab 2 Summary

| Question | Part | Main Concept |
|---|---|---|
| Q1 | A | Class, Object, Methods |
| Q2 | A | Class, Objects, Array of Objects |
| Q3 | A | Constructor |
| Q4 | A | Constructor + Object |
| Q5 | B | Constructor + Array of Objects |
| Q6 | B | Constructor + Array of Objects |

## Key Concepts to Remember

- `class` → creates a class
- `new` → creates an object
- Constructor → initializes an object
- Method → performs an operation
- Array of objects → stores multiple object references
- `for` loop → useful for processing multiple objects

---

## Source

The questions in this document are based on **2401CS521 – Building RESTful APIs with ASP.NET Core, Laboratory/Tutorial Manual, Lab 2: Class, Objects, Array of Objects & Constructor**, Academic Year 2026–27. The manual lists Questions 1–4 as Part A and Questions 5–6 as Part B.
