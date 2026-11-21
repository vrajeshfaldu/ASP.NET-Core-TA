# Lab 2 — Classes, Objects, Constructors, and Arrays

This lab practices defining classes, storing object data in fields, writing member functions, initializing objects with constructors, and managing groups of objects with arrays.

> **How to run the examples:** Each answer below is a separate console-program example. Put one example at a time in `Program.cs` in the corresponding Lab 2 project, then run it. Do not paste all examples into the same file because each has its own `Main` method and class definitions.

## Part A — Classes, member functions, and constructors

### Question 1

**Write a program to create a class named `Faculty` with `ID`, `Name`, `Age`, `Weight`, and `Height` as data members. Create member functions `GetFacultyDetails()` and `DisplayFacultyDetails()`.**

#### Explanation

The `Faculty` class describes one faculty member. Its fields store the member's information. `GetFacultyDetails()` reads values from the console, while `DisplayFacultyDetails()` prints the stored values. The `Main` method creates a `Faculty` object and calls both methods.

#### Answer code

```csharp
using System;

class Faculty
{
    public int ID;
    public string Name = "";
    public int Age;
    public double Weight;
    public double Height;

    public void GetFacultyDetails()
    {
        Console.Write("Enter faculty ID: ");
        ID = Convert.ToInt32(Console.ReadLine());

        Console.Write("Enter faculty name: ");
        Name = Console.ReadLine() ?? "";

        Console.Write("Enter age: ");
        Age = Convert.ToInt32(Console.ReadLine());

        Console.Write("Enter weight in kg: ");
        Weight = Convert.ToDouble(Console.ReadLine());

        Console.Write("Enter height in cm: ");
        Height = Convert.ToDouble(Console.ReadLine());
    }

    public void DisplayFacultyDetails()
    {
        Console.WriteLine("\nFaculty Details");
        Console.WriteLine($"ID: {ID}");
        Console.WriteLine($"Name: {Name}");
        Console.WriteLine($"Age: {Age}");
        Console.WriteLine($"Weight: {Weight:F1} kg");
        Console.WriteLine($"Height: {Height:F1} cm");
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

---

### Question 2

**Write a program to create a class named `Employee` with `Emp_ID`, `Name`, `Department`, `Designation`, and `Salary` as data members. Create `GetEmpDetails()` and `DisplayEmpDetails()` member functions for five different employee objects.**

#### Explanation

Each `Employee` object stores one employee's details. The program creates an array with five employee objects, calls `GetEmpDetails()` on each object to collect input, and then calls `DisplayEmpDetails()` to print all five records.

#### Answer code

```csharp
using System;

class Employee
{
    public int Emp_ID;
    public string Name = "";
    public string Department = "";
    public string Designation = "";
    public decimal Salary;

    public void GetEmpDetails()
    {
        Console.Write("Enter employee ID: ");
        Emp_ID = Convert.ToInt32(Console.ReadLine());

        Console.Write("Enter name: ");
        Name = Console.ReadLine() ?? "";

        Console.Write("Enter department: ");
        Department = Console.ReadLine() ?? "";

        Console.Write("Enter designation: ");
        Designation = Console.ReadLine() ?? "";

        Console.Write("Enter salary: ");
        Salary = Convert.ToDecimal(Console.ReadLine());
    }

    public void DisplayEmpDetails()
    {
        Console.WriteLine($"{Emp_ID,-10}{Name,-20}{Department,-18}{Designation,-20}{Salary,12:F2}");
    }
}

class Program
{
    static void Main()
    {
        Employee[] employees = new Employee[5];

        for (int i = 0; i < employees.Length; i++)
        {
            Console.WriteLine($"\nEnter details for employee {i + 1}");
            employees[i] = new Employee();
            employees[i].GetEmpDetails();
        }

        Console.WriteLine("\nEmployee Report");
        Console.WriteLine($"{"ID",-10}{"Name",-20}{"Department",-18}{"Designation",-20}{"Salary",12}");
        Console.WriteLine(new string('-', 80));

        foreach (Employee employee in employees)
        {
            employee.DisplayEmpDetails();
        }
    }
}
```

---

### Question 3

**Write a program to calculate the volume of a cube using a constructor.**

#### Explanation

A constructor runs when an object is created. This `Cube` constructor receives the side length and stores it in the object. The `CalculateVolume()` method uses the formula `side × side × side`.

#### Answer code

```csharp
using System;

class Cube
{
    private double side;

    public Cube(double sideLength)
    {
        side = sideLength;
    }

    public double CalculateVolume()
    {
        return side * side * side;
    }
}

class Program
{
    static void Main()
    {
        Console.Write("Enter the side length of the cube: ");
        double side = Convert.ToDouble(Console.ReadLine());

        Cube cube = new Cube(side);
        Console.WriteLine($"Volume of the cube: {cube.CalculateVolume():F2}");
    }
}
```

---

### Question 4

**A car dealership wants to display details of premium sports cars. Create a `Car` object with `Make`, `Model`, `Year`, `FuelType`, and `Horsepower` details using a constructor.**

#### Explanation

The `Car` constructor accepts all five values and initializes the object's fields. The `DisplayDetails()` method prints the initialized values. This example creates and displays one premium sports car.

#### Answer code

```csharp
using System;

class Car
{
    public string Make;
    public string Model;
    public int Year;
    public string FuelType;
    public int Horsepower;

    public Car(string make, string model, int year, string fuelType, int horsepower)
    {
        Make = make;
        Model = model;
        Year = year;
        FuelType = fuelType;
        Horsepower = horsepower;
    }

    public void DisplayDetails()
    {
        Console.WriteLine("Premium Sports Car");
        Console.WriteLine($"Make: {Make}");
        Console.WriteLine($"Model: {Model}");
        Console.WriteLine($"Year: {Year}");
        Console.WriteLine($"Fuel type: {FuelType}");
        Console.WriteLine($"Horsepower: {Horsepower} hp");
    }
}

class Program
{
    static void Main()
    {
        Car car = new Car("Porsche", "911 Carrera", 2025, "Petrol", 388);
        car.DisplayDetails();
    }
}
```

## Part B — Arrays of objects

### Question 5

**Write a program to manage supermarket inventory. Create an `Item` class with `Item_Code`, `Item_Name`, and `Stock_Quantity`. Use a constructor to initialize objects and an array of objects to store and display the details of 10 different items.**

#### Explanation

The `Item` constructor initializes each item's code, name, and stock quantity. The program creates an array for ten `Item` objects, reads the details for each item, constructs the object, and then displays all objects in a formatted table.

#### Answer code

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

    public void DisplayDetails()
    {
        Console.WriteLine($"{Item_Code,-12}{Item_Name,-25}{Stock_Quantity,15}");
    }
}

class Program
{
    static void Main()
    {
        Item[] items = new Item[10];

        for (int i = 0; i < items.Length; i++)
        {
            Console.WriteLine($"\nEnter details for item {i + 1}");

            Console.Write("Item code: ");
            int code = Convert.ToInt32(Console.ReadLine());

            Console.Write("Item name: ");
            string name = Console.ReadLine() ?? "";

            Console.Write("Stock quantity: ");
            int quantity = Convert.ToInt32(Console.ReadLine());

            items[i] = new Item(code, name, quantity);
        }

        Console.WriteLine("\nSupermarket Inventory");
        Console.WriteLine($"{"Code",-12}{"Item name",-25}{"Stock quantity",15}");
        Console.WriteLine(new string('-', 52));

        foreach (Item item in items)
        {
            item.DisplayDetails();
        }
    }
}
```

---

### Question 6

**A clinic management system needs to store patient records. Create a `Patient` class containing `Patient_ID`, `Name`, `Age`, and `Disease`. Use an array of objects to accept details for multiple patients and display a formatted summary report of all patients.**

#### Explanation

The program asks how many patient records to store, then creates an array of that size. Each `Patient` object is initialized through its constructor. The report uses fixed-width columns so patient information is easy to read.

#### Answer code

```csharp
using System;

class Patient
{
    public int Patient_ID;
    public string Name;
    public int Age;
    public string Disease;

    public Patient(int patientId, string name, int age, string disease)
    {
        Patient_ID = patientId;
        Name = name;
        Age = age;
        Disease = disease;
    }

    public void DisplayDetails()
    {
        Console.WriteLine($"{Patient_ID,-14}{Name,-24}{Age,-8}{Disease}");
    }
}

class Program
{
    static void Main()
    {
        Console.Write("Enter number of patients: ");
        int count = Convert.ToInt32(Console.ReadLine());

        if (count <= 0)
        {
            Console.WriteLine("The number of patients must be greater than zero.");
            return;
        }

        Patient[] patients = new Patient[count];

        for (int i = 0; i < patients.Length; i++)
        {
            Console.WriteLine($"\nEnter details for patient {i + 1}");

            Console.Write("Patient ID: ");
            int id = Convert.ToInt32(Console.ReadLine());

            Console.Write("Name: ");
            string name = Console.ReadLine() ?? "";

            Console.Write("Age: ");
            int age = Convert.ToInt32(Console.ReadLine());

            Console.Write("Disease: ");
            string disease = Console.ReadLine() ?? "";

            patients[i] = new Patient(id, name, age, disease);
        }

        Console.WriteLine("\nClinic Patient Summary");
        Console.WriteLine($"{"Patient ID",-14}{"Name",-24}{"Age",-8}{"Disease"}");
        Console.WriteLine(new string('-', 65));

        foreach (Patient patient in patients)
        {
            patient.DisplayDetails();
        }
    }
}
```

## Concepts practiced

- **Class:** A definition for the data and behavior of a type of object.
- **Object:** An instance created from a class.
- **Field:** A variable that stores data inside an object.
- **Member function:** A method that belongs to a class.
- **Constructor:** A special method called when an object is created; it initializes the object.
- **Array of objects:** An array whose elements hold references to objects of the same class.
