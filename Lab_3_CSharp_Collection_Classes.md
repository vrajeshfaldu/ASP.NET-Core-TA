# Lab 3 – Collection Classes

**Course:** 2401CS521 – Building RESTful APIs with ASP.NET Core  
**University:** Darshan University  
**Lab:** 3  
**Topic:** Collection Classes

> **Note:** This guide is based on the official Lab 3 questions in the 2401CS521 Laboratory/Tutorial Manual. The uploaded `.slnx` files identify the six projects (`lab3A1` to `lab3C2`), but the C# source files were not included, so the code below is a clean student-friendly implementation of the manual questions.

---

# 1. Learning Objectives

After completing this lab, students should be able to:

- Understand collection classes in C#.
- Use `List<T>` to store multiple objects.
- Add, display, search, update and delete records.
- Use `Dictionary<TKey,TValue>` for key-value data.
- Use `HashSet<T>` to store unique values.
- Use `ContainsKey()` to check whether a dictionary key exists.
- Use `UnionWith()` to merge HashSet values.
- Create menu-driven console applications.

---

# 2. Basic Theory

## 2.1 Collection

A collection stores multiple values or objects together.

Common C# collections used in this lab:

| Collection | Use |
|---|---|
| `List<T>` | Ordered collection of objects |
| `Dictionary<TKey,TValue>` | Key-value data |
| `HashSet<T>` | Unique values |

---

## 2.2 List<T>

`List<T>` is a dynamic collection.

```csharp
List<string> names = new List<string>();

names.Add("Rahul");
names.Add("Amit");

Console.WriteLine(names[0]);
```

Important methods:

- `Add()` → adds an item
- `Remove()` → removes an item
- `Find()` → finds an item
- `Count` → returns number of items
- `Clear()` → removes all items

---

## 2.3 Dictionary<TKey,TValue>

A dictionary stores data using a **key** and a **value**.

```csharp
Dictionary<int, string> students = new Dictionary<int, string>();

students.Add(101, "Rahul");
students.Add(102, "Amit");

Console.WriteLine(students[101]);
```

Here:

- `101` → key
- `"Rahul"` → value

### ContainsKey()

```csharp
if (students.ContainsKey(101))
{
    Console.WriteLine("Student exists");
}
```

`ContainsKey()` checks whether a particular key exists.

---

## 2.4 HashSet<T>

`HashSet<T>` stores **unique values**.

```csharp
HashSet<string> permissions = new HashSet<string>();

permissions.Add("Read");
permissions.Add("Write");
permissions.Add("Read");
```

The second `"Read"` is not stored again.

### UnionWith()

```csharp
HashSet<string> role1 = new HashSet<string>
{
    "Read",
    "Write"
};

HashSet<string> role2 = new HashSet<string>
{
    "Delete",
    "Read"
};

role1.UnionWith(role2);
```

Now `role1` contains:

```text
Read
Write
Delete
```

---

# Part A

# Question 1 – Student Management using List<Student>

## Question

Develop a menu-driven console application to manage student records using `List<Student>`. Implement:

- Add
- Display
- Search
- Update
- Delete

## Code

```csharp
using System;
using System.Collections.Generic;

class Student
{
    public int Id;
    public string Name;
    public double Marks;

    public Student(int id, string name, double marks)
    {
        Id = id;
        Name = name;
        Marks = marks;
    }

    public void Display()
    {
        Console.WriteLine($"ID    : {Id}");
        Console.WriteLine($"Name  : {Name}");
        Console.WriteLine($"Marks : {Marks}");
        Console.WriteLine();
    }
}

class Program
{
    static List<Student> students = new List<Student>();

    static void AddStudent()
    {
        Console.Write("Enter ID: ");
        int id = Convert.ToInt32(Console.ReadLine());

        Console.Write("Enter Name: ");
        string name = Console.ReadLine();

        Console.Write("Enter Marks: ");
        double marks = Convert.ToDouble(Console.ReadLine());

        students.Add(new Student(id, name, marks));

        Console.WriteLine("Student added successfully.");
    }

    static void DisplayStudents()
    {
        if (students.Count == 0)
        {
            Console.WriteLine("No students found.");
            return;
        }

        foreach (Student student in students)
        {
            student.Display();
        }
    }

    static void SearchStudent()
    {
        Console.Write("Enter ID to search: ");
        int id = Convert.ToInt32(Console.ReadLine());

        Student student = students.Find(s => s.Id == id);

        if (student != null)
        {
            student.Display();
        }
        else
        {
            Console.WriteLine("Student not found.");
        }
    }

    static void UpdateStudent()
    {
        Console.Write("Enter ID to update: ");
        int id = Convert.ToInt32(Console.ReadLine());

        Student student = students.Find(s => s.Id == id);

        if (student != null)
        {
            Console.Write("Enter new name: ");
            student.Name = Console.ReadLine();

            Console.Write("Enter new marks: ");
            student.Marks = Convert.ToDouble(Console.ReadLine());

            Console.WriteLine("Student updated successfully.");
        }
        else
        {
            Console.WriteLine("Student not found.");
        }
    }

    static void DeleteStudent()
    {
        Console.Write("Enter ID to delete: ");
        int id = Convert.ToInt32(Console.ReadLine());

        Student student = students.Find(s => s.Id == id);

        if (student != null)
        {
            students.Remove(student);
            Console.WriteLine("Student deleted successfully.");
        }
        else
        {
            Console.WriteLine("Student not found.");
        }
    }

    static void Main()
    {
        while (true)
        {
            Console.WriteLine("\n--- Student Management ---");
            Console.WriteLine("1. Add");
            Console.WriteLine("2. Display");
            Console.WriteLine("3. Search");
            Console.WriteLine("4. Update");
            Console.WriteLine("5. Delete");
            Console.WriteLine("6. Exit");

            Console.Write("Enter choice: ");
            int choice = Convert.ToInt32(Console.ReadLine());

            switch (choice)
            {
                case 1:
                    AddStudent();
                    break;

                case 2:
                    DisplayStudents();
                    break;

                case 3:
                    SearchStudent();
                    break;

                case 4:
                    UpdateStudent();
                    break;

                case 5:
                    DeleteStudent();
                    break;

                case 6:
                    return;

                default:
                    Console.WriteLine("Invalid choice.");
                    break;
            }
        }
    }
}
```

## Important Concepts

### Create List

```csharp
static List<Student> students = new List<Student>();
```

Creates a list that stores `Student` objects.

### Add

```csharp
students.Add(new Student(id, name, marks));
```

Adds a new student.

### Search

```csharp
students.Find(s => s.Id == id);
```

Finds a student using the ID.

### Update

```csharp
student.Name = Console.ReadLine();
```

Changes existing student data.

### Delete

```csharp
students.Remove(student);
```

Removes the student from the list.

---

# Question 2 – Shopping Cart using List<CartItem>

## Question

Implement a Shopping Cart system using `List<CartItem>` that supports:

- Adding items
- Removing items
- Viewing cart details
- Calculating total amount
- Applying discount

## Code

```csharp
using System;
using System.Collections.Generic;

class CartItem
{
    public int Id;
    public string Name;
    public double Price;
    public int Quantity;

    public CartItem(int id, string name, double price, int quantity)
    {
        Id = id;
        Name = name;
        Price = price;
        Quantity = quantity;
    }

    public double GetTotal()
    {
        return Price * Quantity;
    }

    public void Display()
    {
        Console.WriteLine($"ID       : {Id}");
        Console.WriteLine($"Name     : {Name}");
        Console.WriteLine($"Price    : {Price}");
        Console.WriteLine($"Quantity : {Quantity}");
        Console.WriteLine($"Total    : {GetTotal()}");
        Console.WriteLine();
    }
}

class Program
{
    static List<CartItem> cart = new List<CartItem>();

    static void AddItem()
    {
        Console.Write("Enter Item ID: ");
        int id = Convert.ToInt32(Console.ReadLine());

        Console.Write("Enter Item Name: ");
        string name = Console.ReadLine();

        Console.Write("Enter Price: ");
        double price = Convert.ToDouble(Console.ReadLine());

        Console.Write("Enter Quantity: ");
        int quantity = Convert.ToInt32(Console.ReadLine());

        cart.Add(new CartItem(id, name, price, quantity));

        Console.WriteLine("Item added.");
    }

    static void RemoveItem()
    {
        Console.Write("Enter Item ID to remove: ");
        int id = Convert.ToInt32(Console.ReadLine());

        CartItem item = cart.Find(x => x.Id == id);

        if (item != null)
        {
            cart.Remove(item);
            Console.WriteLine("Item removed.");
        }
        else
        {
            Console.WriteLine("Item not found.");
        }
    }

    static void ViewCart()
    {
        if (cart.Count == 0)
        {
            Console.WriteLine("Cart is empty.");
            return;
        }

        foreach (CartItem item in cart)
        {
            item.Display();
        }
    }

    static void CalculateTotal()
    {
        double total = 0;

        foreach (CartItem item in cart)
        {
            total += item.GetTotal();
        }

        Console.WriteLine($"Total Amount : {total}");

        Console.Write("Enter discount percentage: ");
        double discount = Convert.ToDouble(Console.ReadLine());

        double discountAmount = total * discount / 100;
        double finalAmount = total - discountAmount;

        Console.WriteLine($"Discount     : {discountAmount}");
        Console.WriteLine($"Final Amount : {finalAmount}");
    }

    static void Main()
    {
        while (true)
        {
            Console.WriteLine("\n--- Shopping Cart ---");
            Console.WriteLine("1. Add Item");
            Console.WriteLine("2. Remove Item");
            Console.WriteLine("3. View Cart");
            Console.WriteLine("4. Calculate Total");
            Console.WriteLine("5. Exit");

            Console.Write("Enter choice: ");
            int choice = Convert.ToInt32(Console.ReadLine());

            switch (choice)
            {
                case 1:
                    AddItem();
                    break;

                case 2:
                    RemoveItem();
                    break;

                case 3:
                    ViewCart();
                    break;

                case 4:
                    CalculateTotal();
                    break;

                case 5:
                    return;

                default:
                    Console.WriteLine("Invalid choice.");
                    break;
            }
        }
    }
}
```

## Important Formula

```text
Item Total = Price × Quantity

Discount Amount = Total × Discount Percentage / 100

Final Amount = Total - Discount Amount
```

---

# Part B

# Question 3 – Student Grade Book using Dictionary

## Question

Create a Student Grade Book using `Dictionary<int, Student>` where each student record contains subject-wise marks.

Implement:

- Add/update student details
- Calculate percentage
- Display reports

## Code

```csharp
using System;
using System.Collections.Generic;

class Student
{
    public int Id;
    public string Name;
    public Dictionary<string, double> Marks;

    public Student(int id, string name)
    {
        Id = id;
        Name = name;
        Marks = new Dictionary<string, double>();
    }

    public double CalculatePercentage()
    {
        if (Marks.Count == 0)
            return 0;

        double total = 0;

        foreach (double mark in Marks.Values)
        {
            total += mark;
        }

        return total / Marks.Count;
    }

    public void Display()
    {
        Console.WriteLine($"ID   : {Id}");
        Console.WriteLine($"Name : {Name}");

        foreach (var mark in Marks)
        {
            Console.WriteLine($"{mark.Key} : {mark.Value}");
        }

        Console.WriteLine($"Percentage : {CalculatePercentage()}");
        Console.WriteLine();
    }
}

class Program
{
    static Dictionary<int, Student> students =
        new Dictionary<int, Student>();

    static void AddStudent()
    {
        Console.Write("Enter Student ID: ");
        int id = Convert.ToInt32(Console.ReadLine());

        Console.Write("Enter Name: ");
        string name = Console.ReadLine();

        if (students.ContainsKey(id))
        {
            students[id].Name = name;
            Console.WriteLine("Student name updated.");
        }
        else
        {
            students.Add(id, new Student(id, name));
            Console.WriteLine("Student added.");
        }

        Console.Write("Enter number of subjects: ");
        int n = Convert.ToInt32(Console.ReadLine());

        for (int i = 0; i < n; i++)
        {
            Console.Write("Enter Subject: ");
            string subject = Console.ReadLine();

            Console.Write("Enter Marks: ");
            double marks = Convert.ToDouble(Console.ReadLine());

            students[id].Marks[subject] = marks;
        }
    }

    static void DisplayStudent()
    {
        Console.Write("Enter Student ID: ");
        int id = Convert.ToInt32(Console.ReadLine());

        if (students.ContainsKey(id))
        {
            students[id].Display();
        }
        else
        {
            Console.WriteLine("Student not found.");
        }
    }

    static void DisplayAll()
    {
        foreach (Student student in students.Values)
        {
            student.Display();
        }
    }

    static void Main()
    {
        while (true)
        {
            Console.WriteLine("\n--- Student Grade Book ---");
            Console.WriteLine("1. Add / Update Student");
            Console.WriteLine("2. Display Student");
            Console.WriteLine("3. Display All");
            Console.WriteLine("4. Exit");

            Console.Write("Enter choice: ");
            int choice = Convert.ToInt32(Console.ReadLine());

            switch (choice)
            {
                case 1:
                    AddStudent();
                    break;

                case 2:
                    DisplayStudent();
                    break;

                case 3:
                    DisplayAll();
                    break;

                case 4:
                    return;

                default:
                    Console.WriteLine("Invalid choice.");
                    break;
            }
        }
    }
}
```

## Key Concept – ContainsKey()

```csharp
if (students.ContainsKey(id))
```

This checks whether the student ID already exists.

Example:

```text
ID = 101

Dictionary:
101 → Rahul
102 → Amit
```

`students.ContainsKey(101)` → `true`

`students.ContainsKey(105)` → `false`

---

# Question 4 – Product Catalog and Inventory using Dictionary

## Question

Develop a Product Catalog and Inventory Management System using:

```csharp
Dictionary<string, Product>
```

Features:

- Add products
- Update stock
- Search products
- Generate bills

## Code

```csharp
using System;
using System.Collections.Generic;

class Product
{
    public string Code;
    public string Name;
    public double Price;
    public int Stock;

    public Product(string code, string name, double price, int stock)
    {
        Code = code;
        Name = name;
        Price = price;
        Stock = stock;
    }

    public void Display()
    {
        Console.WriteLine($"Code  : {Code}");
        Console.WriteLine($"Name  : {Name}");
        Console.WriteLine($"Price : {Price}");
        Console.WriteLine($"Stock : {Stock}");
        Console.WriteLine();
    }
}

class Program
{
    static Dictionary<string, Product> products =
        new Dictionary<string, Product>();

    static void AddProduct()
    {
        Console.Write("Enter Product Code: ");
        string code = Console.ReadLine();

        Console.Write("Enter Product Name: ");
        string name = Console.ReadLine();

        Console.Write("Enter Price: ");
        double price = Convert.ToDouble(Console.ReadLine());

        Console.Write("Enter Stock: ");
        int stock = Convert.ToInt32(Console.ReadLine());

        if (products.ContainsKey(code))
        {
            Console.WriteLine("Product already exists.");
            return;
        }

        products.Add(code, new Product(code, name, price, stock));

        Console.WriteLine("Product added.");
    }

    static void UpdateStock()
    {
        Console.Write("Enter Product Code: ");
        string code = Console.ReadLine();

        if (products.ContainsKey(code))
        {
            Console.Write("Enter new stock: ");
            products[code].Stock =
                Convert.ToInt32(Console.ReadLine());

            Console.WriteLine("Stock updated.");
        }
        else
        {
            Console.WriteLine("Product not found.");
        }
    }

    static void SearchProduct()
    {
        Console.Write("Enter Product Code: ");
        string code = Console.ReadLine();

        if (products.ContainsKey(code))
        {
            products[code].Display();
        }
        else
        {
            Console.WriteLine("Product not found.");
        }
    }

    static void GenerateBill()
    {
        Console.Write("Enter Product Code: ");
        string code = Console.ReadLine();

        if (!products.ContainsKey(code))
        {
            Console.WriteLine("Product not found.");
            return;
        }

        Product product = products[code];

        Console.Write("Enter Quantity: ");
        int quantity = Convert.ToInt32(Console.ReadLine());

        if (quantity > product.Stock)
        {
            Console.WriteLine("Insufficient stock.");
            return;
        }

        double total = product.Price * quantity;

        product.Stock -= quantity;

        Console.WriteLine("\n--- Bill ---");
        Console.WriteLine($"Product  : {product.Name}");
        Console.WriteLine($"Price    : {product.Price}");
        Console.WriteLine($"Quantity : {quantity}");
        Console.WriteLine($"Total    : {total}");
    }

    static void Main()
    {
        while (true)
        {
            Console.WriteLine("\n--- Product Inventory ---");
            Console.WriteLine("1. Add Product");
            Console.WriteLine("2. Update Stock");
            Console.WriteLine("3. Search Product");
            Console.WriteLine("4. Generate Bill");
            Console.WriteLine("5. Exit");

            Console.Write("Enter choice: ");
            int choice = Convert.ToInt32(Console.ReadLine());

            switch (choice)
            {
                case 1:
                    AddProduct();
                    break;

                case 2:
                    UpdateStock();
                    break;

                case 3:
                    SearchProduct();
                    break;

                case 4:
                    GenerateBill();
                    break;

                case 5:
                    return;

                default:
                    Console.WriteLine("Invalid choice.");
                    break;
            }
        }
    }
}
```

---

# Part C

# Question 5 – Role-Based Permission System using HashSet

## Question

Implement a Role-Based Permission System using `HashSet<string>` to manage user permissions.

Support:

- Role assignment
- Permission checking
- Merging permissions

## Code

```csharp
using System;
using System.Collections.Generic;

class Program
{
    static Dictionary<string, HashSet<string>> roles =
        new Dictionary<string, HashSet<string>>();

    static void AddRole()
    {
        Console.Write("Enter Role Name: ");
        string roleName = Console.ReadLine();

        if (roles.ContainsKey(roleName))
        {
            Console.WriteLine("Role already exists.");
            return;
        }

        roles.Add(roleName, new HashSet<string>());

        Console.WriteLine("Role added.");
    }

    static void AddPermission()
    {
        Console.Write("Enter Role Name: ");
        string roleName = Console.ReadLine();

        if (!roles.ContainsKey(roleName))
        {
            Console.WriteLine("Role not found.");
            return;
        }

        Console.Write("Enter Permission: ");
        string permission = Console.ReadLine();

        roles[roleName].Add(permission);

        Console.WriteLine("Permission added.");
    }

    static void CheckPermission()
    {
        Console.Write("Enter Role Name: ");
        string roleName = Console.ReadLine();

        if (!roles.ContainsKey(roleName))
        {
            Console.WriteLine("Role not found.");
            return;
        }

        Console.Write("Enter Permission: ");
        string permission = Console.ReadLine();

        if (roles[roleName].Contains(permission))
        {
            Console.WriteLine("Permission allowed.");
        }
        else
        {
            Console.WriteLine("Permission denied.");
        }
    }

    static void MergePermissions()
    {
        Console.Write("Enter first role: ");
        string role1 = Console.ReadLine();

        Console.Write("Enter second role: ");
        string role2 = Console.ReadLine();

        if (!roles.ContainsKey(role1) ||
            !roles.ContainsKey(role2))
        {
            Console.WriteLine("One or both roles not found.");
            return;
        }

        roles[role1].UnionWith(roles[role2]);

        Console.WriteLine("Permissions merged successfully.");
    }

    static void DisplayRoles()
    {
        foreach (var role in roles)
        {
            Console.WriteLine($"\nRole: {role.Key}");

            foreach (string permission in role.Value)
            {
                Console.WriteLine($"Permission: {permission}");
            }
        }
    }

    static void Main()
    {
        while (true)
        {
            Console.WriteLine("\n--- Role-Based Permission System ---");
            Console.WriteLine("1. Add Role");
            Console.WriteLine("2. Add Permission");
            Console.WriteLine("3. Check Permission");
            Console.WriteLine("4. Merge Permissions");
            Console.WriteLine("5. Display Roles");
            Console.WriteLine("6. Exit");

            Console.Write("Enter choice: ");
            int choice = Convert.ToInt32(Console.ReadLine());

            switch (choice)
            {
                case 1:
                    AddRole();
                    break;

                case 2:
                    AddPermission();
                    break;

                case 3:
                    CheckPermission();
                    break;

                case 4:
                    MergePermissions();
                    break;

                case 5:
                    DisplayRoles();
                    break;

                case 6:
                    return;

                default:
                    Console.WriteLine("Invalid choice.");
                    break;
            }
        }
    }
}
```

## Important Concepts

### `roleName`

```csharp
string roleName = Console.ReadLine();
```

This is the **name of the role**, for example:

```text
Admin
Faculty
Student
```

### `role`

In:

```csharp
foreach (var role in roles)
```

`role` represents one dictionary entry.

For example:

```text
role.Key   → Admin
role.Value → HashSet of permissions
```

### Permission

Example:

```text
Read
Write
Delete
Update
```

### Contains()

```csharp
roles[roleName].Contains(permission)
```

Checks whether the role has a particular permission.

### UnionWith()

```csharp
roles[role1].UnionWith(roles[role2]);
```

Combines permissions from the second role into the first role.

---

# Question 6 – Social Networking using HashSet<T>

## Question

Develop core features for a Social Networking application using `HashSet<T>` to manage:

- User interests
- Friends
- Mutual friends
- Interest-based suggestions
- Duplicate tag removal

## Code

```csharp
using System;
using System.Collections.Generic;

class User
{
    public string Name;
    public HashSet<string> Friends;
    public HashSet<string> Interests;

    public User(string name)
    {
        Name = name;
        Friends = new HashSet<string>();
        Interests = new HashSet<string>();
    }
}

class Program
{
    static Dictionary<string, User> users =
        new Dictionary<string, User>();

    static void AddUser()
    {
        Console.Write("Enter user name: ");
        string name = Console.ReadLine();

        if (users.ContainsKey(name))
        {
            Console.WriteLine("User already exists.");
            return;
        }

        users.Add(name, new User(name));

        Console.WriteLine("User added.");
    }

    static void AddFriend()
    {
        Console.Write("Enter first user: ");
        string user1 = Console.ReadLine();

        Console.Write("Enter second user: ");
        string user2 = Console.ReadLine();

        if (!users.ContainsKey(user1) ||
            !users.ContainsKey(user2))
        {
            Console.WriteLine("User not found.");
            return;
        }

        users[user1].Friends.Add(user2);
        users[user2].Friends.Add(user1);

        Console.WriteLine("Friend added.");
    }

    static void AddInterest()
    {
        Console.Write("Enter user name: ");
        string name = Console.ReadLine();

        if (!users.ContainsKey(name))
        {
            Console.WriteLine("User not found.");
            return;
        }

        Console.Write("Enter interest: ");
        string interest = Console.ReadLine();

        users[name].Interests.Add(interest);

        Console.WriteLine("Interest added.");
    }

    static void FindMutualFriends()
    {
        Console.Write("Enter first user: ");
        string user1 = Console.ReadLine();

        Console.Write("Enter second user: ");
        string user2 = Console.ReadLine();

        if (!users.ContainsKey(user1) ||
            !users.ContainsKey(user2))
        {
            Console.WriteLine("User not found.");
            return;
        }

        HashSet<string> mutual =
            new HashSet<string>(users[user1].Friends);

        mutual.IntersectWith(users[user2].Friends);

        Console.WriteLine("\n--- Mutual Friends ---");

        foreach (string friend in mutual)
        {
            Console.WriteLine(friend);
        }
    }

    static void DisplayUser()
    {
        Console.Write("Enter user name: ");
        string name = Console.ReadLine();

        if (!users.ContainsKey(name))
        {
            Console.WriteLine("User not found.");
            return;
        }

        Console.WriteLine($"\nUser: {name}");

        Console.WriteLine("Friends:");
        foreach (string friend in users[name].Friends)
        {
            Console.WriteLine(friend);
        }

        Console.WriteLine("Interests:");
        foreach (string interest in users[name].Interests)
        {
            Console.WriteLine(interest);
        }
    }

    static void Main()
    {
        while (true)
        {
            Console.WriteLine("\n--- Social Networking ---");
            Console.WriteLine("1. Add User");
            Console.WriteLine("2. Add Friend");
            Console.WriteLine("3. Add Interest");
            Console.WriteLine("4. Find Mutual Friends");
            Console.WriteLine("5. Display User");
            Console.WriteLine("6. Exit");

            Console.Write("Enter choice: ");
            int choice = Convert.ToInt32(Console.ReadLine());

            switch (choice)
            {
                case 1:
                    AddUser();
                    break;

                case 2:
                    AddFriend();
                    break;

                case 3:
                    AddInterest();
                    break;

                case 4:
                    FindMutualFriends();
                    break;

                case 5:
                    DisplayUser();
                    break;

                case 6:
                    return;

                default:
                    Console.WriteLine("Invalid choice.");
                    break;
            }
        }
    }
}
```

## Important HashSet Operations

### Add

```csharp
friends.Add("Rahul");
```

Adds a friend.

### Contains

```csharp
friends.Contains("Rahul");
```

Checks whether Rahul exists.

### UnionWith

Combines two sets.

```csharp
set1.UnionWith(set2);
```

### IntersectWith

Finds common values.

```csharp
set1.IntersectWith(set2);
```

Example:

```text
Friends of Rahul:
Amit
Priya
Jay

Friends of Amit:
Priya
Jay
Neha

Mutual Friends:
Priya
Jay
```

---

# 3. Important Differences

## List vs Dictionary vs HashSet

| Collection | Stores | Duplicate Values | Access |
|---|---|---|---|
| `List<T>` | Objects/items | Allowed | Index |
| `Dictionary<TKey,TValue>` | Key + Value | Keys must be unique | Key |
| `HashSet<T>` | Unique values | Not allowed | Value |

---

# 4. Common Methods

## List

```csharp
Add()
Remove()
Find()
Count
Clear()
```

## Dictionary

```csharp
Add()
ContainsKey()
Remove()
Count
```

## HashSet

```csharp
Add()
Remove()
Contains()
UnionWith()
IntersectWith()
```

---

# 5. Common Beginner Mistakes

### Mistake 1: Forgetting to import collections

Use:

```csharp
using System.Collections.Generic;
```

---

### Mistake 2: Adding a duplicate dictionary key

Incorrect:

```csharp
Dictionary<int, string> data =
    new Dictionary<int, string>();

data.Add(101, "Rahul");
data.Add(101, "Amit");
```

The same key cannot be added again.

Use:

```csharp
if (!data.ContainsKey(101))
{
    data.Add(101, "Rahul");
}
```

---

### Mistake 3: Confusing `ContainsKey()` and `Contains()`

For Dictionary:

```csharp
students.ContainsKey(101);
```

For HashSet:

```csharp
permissions.Contains("Read");
```

---

### Mistake 4: Thinking HashSet stores duplicates

```csharp
HashSet<string> names = new HashSet<string>();

names.Add("Rahul");
names.Add("Rahul");
```

Only one `"Rahul"` is stored.

---

# 6. Viva Questions

### Q1. What is a collection?

A collection is used to store multiple values or objects.

### Q2. What is List<T>?

`List<T>` is a dynamic collection that stores multiple items.

### Q3. What is Dictionary<TKey,TValue>?

It stores data in key-value pairs.

### Q4. What is HashSet<T>?

It stores unique values and does not allow duplicate values.

### Q5. What does Add() do?

It adds an element to a collection.

### Q6. What does ContainsKey() do?

It checks whether a specified key exists in a dictionary.

### Q7. What does Contains() do?

It checks whether a specified value exists in a collection such as a HashSet.

### Q8. What is UnionWith()?

It combines the elements of another collection into a HashSet.

### Q9. What is IntersectWith()?

It keeps only the elements that are common between two sets.

### Q10. Can Dictionary have duplicate keys?

No. Dictionary keys must be unique.

### Q11. Can HashSet have duplicate values?

No.

### Q12. Can List have duplicate values?

Yes.

### Q13. Why is List<T> called dynamic?

Its size can grow or shrink as items are added or removed.

### Q14. What is a key in Dictionary?

A key uniquely identifies a value.

### Q15. Why use HashSet for permissions?

Because the same permission should not be stored multiple times.

---

# 7. Quick Revision

```text
List<T>
  ↓
Stores multiple items
  ↓
Add / Remove / Find / Count


Dictionary<TKey,TValue>
  ↓
Key + Value
  ↓
ContainsKey()


HashSet<T>
  ↓
Unique values
  ↓
Contains()
UnionWith()
IntersectWith()
```

---

# 8. Lab 3 Summary

| Question | Part | Main Concept |
|---|---|---|
| Q1 | A | List<Student> + CRUD-like operations |
| Q2 | A | List<CartItem> + Shopping Cart |
| Q3 | B | Dictionary<int, Student> |
| Q4 | B | Dictionary<string, Product> |
| Q5 | C | HashSet<string> + Permissions |
| Q6 | C | HashSet<T> + Social Networking |

## Key Concepts to Remember

- `List<T>` → ordered collection
- `Dictionary<TKey,TValue>` → key-value collection
- `HashSet<T>` → unique values
- `Add()` → add an item
- `Remove()` → remove an item
- `ContainsKey()` → check dictionary key
- `Contains()` → check collection value
- `UnionWith()` → combine sets
- `IntersectWith()` → find common values
- `foreach` → process collection elements

---

## Source

Questions are based on **2401CS521 – Building RESTful APIs with ASP.NET Core, Laboratory/Tutorial Manual, Lab 3: Collection Classes, Academic Year 2026–27**. The manual lists six Lab 3 exercises covering `List<T>`, `Dictionary<TKey,TValue>`, and `HashSet<T>`.
