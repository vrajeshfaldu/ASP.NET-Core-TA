# Lab 6: EF Core Entities, DbContext, and Relationships

## 1. Entity Framework Core Concepts

### Entity

An entity is a C# class that represents a database table. EF Core maps the `Course` entity to a table named `Courses`.

### Primary Key

A primary key uniquely identifies each row in a table.

```csharp
public int CourseId { get; set; }
```

EF Core conventionally treats a property named `Id` or `<EntityName>Id` as the primary key.

### Foreign Key

A foreign key references the primary key of another table.

```csharp
public int FacultyId { get; set; }
```

In `Course`, `FacultyId` references `UserId` in the `Users` table.

### Navigation Property

A navigation property connects an entity to related entities.

```csharp
public User Faculty { get; set; }
public ICollection<Assignment> Assignments { get; set; }
```

- `Faculty` represents one related faculty user.
- `Assignments` represents all assignments belonging to a course.

### DbContext

A `DbContext` represents the session used to query and save entities.

```csharp
public class AppDbContext : DbContext
{
}
```

### Delete Behavior

EF Core supports several delete behaviors:

| Delete behavior | Result |
|---|---|
| `Cascade` | Deletes dependent records automatically |
| `Restrict` | Prevents deletion if dependent records exist |
| `NoAction` | The database does not automatically delete dependent records |
| `SetNull` | Sets the foreign key to `NULL` |
| `SetDefault` | Sets the foreign key to its default value |

This lab uses `DeleteBehavior.NoAction` to prevent accidental deletion of related records.

---

## 2. Entity Classes

Create a folder named `Models`.

### Role.cs

```csharp
namespace lab6.Models
{
    public class Role
    {
        public int RoleId { get; set; }
        public string RoleName { get; set; }

        public ICollection<User> Users { get; set; } = new List<User>();
    }
}
```

### User.cs

```csharp
namespace lab6.Models
{
    public class User
    {
        public int UserId { get; set; }
        public int RoleId { get; set; }

        public string FullName { get; set; }
        public string Email { get; set; }
        public string PasswordHash { get; set; }
        public string? ProfilePicturePath { get; set; }

        public bool IsActive { get; set; }
        public bool IsDeleted { get; set; }

        public DateTime CreatedAt { get; set; }
        public DateTime? UpdatedAt { get; set; }

        public Role Role { get; set; }

        public ICollection<Course> Courses { get; set; } = new List<Course>();
        public ICollection<Assignment> Assignments { get; set; } = new List<Assignment>();
        public ICollection<Submission> Submissions { get; set; } = new List<Submission>();
    }
}
```

### Course.cs

```csharp
namespace lab6.Models
{
    public class Course
    {
        public int CourseId { get; set; }

        public string CourseCode { get; set; }
        public string CourseTitle { get; set; }

        public int FacultyId { get; set; }
        public int Credits { get; set; }

        public bool IsDeleted { get; set; }

        public DateTime CreatedAt { get; set; }
        public DateTime? UpdatedAt { get; set; }

        public int? CreatedBy { get; set; }
        public int? UpdatedBy { get; set; }

        public User Faculty { get; set; }

        public User? CreatedByUser { get; set; }
        public User? UpdatedByUser { get; set; }

        public ICollection<Assignment> Assignments { get; set; } = new List<Assignment>();
    }
}
```

### Assignment.cs

```csharp
namespace lab6.Models
{
    public class Assignment
    {
        public int AssignmentId { get; set; }
        public int CourseId { get; set; }

        public string Title { get; set; }
        public string? Description { get; set; }

        public DateTime DueDate { get; set; }
        public int MaxMarks { get; set; }

        public bool IsDeleted { get; set; }

        public DateTime CreatedAt { get; set; }
        public DateTime? UpdatedAt { get; set; }

        public int CreatedBy { get; set; }
        public int? UpdatedBy { get; set; }

        public Course Course { get; set; }

        public User CreatedByUser { get; set; }
        public User? UpdatedByUser { get; set; }

        public ICollection<Submission> Submissions { get; set; } = new List<Submission>();
    }
}
```

### Submission.cs

```csharp
namespace lab6.Models
{
    public class Submission
    {
        public int SubmissionId { get; set; }

        public int AssignmentId { get; set; }
        public int StudentId { get; set; }

        public string FilePath { get; set; }

        public int? MarksObtained { get; set; }
        public string? Remarks { get; set; }
        public string Status { get; set; }

        public DateTime SubmittedAt { get; set; }
        public DateTime? GradedAt { get; set; }

        public int? GradedBy { get; set; }

        public bool IsDeleted { get; set; }

        public DateTime CreatedAt { get; set; }
        public DateTime? UpdatedAt { get; set; }

        public Assignment Assignment { get; set; }
        public User Student { get; set; }
        public User? GradedByUser { get; set; }
    }
}
```

---

## 3. Create the DbContext

Create a folder named `Data`.

### AppDbContext.cs

```csharp
using lab6.Models;
using Microsoft.EntityFrameworkCore;

namespace lab6.Data
{
    public class AppDbContext : DbContext
    {
        public AppDbContext(DbContextOptions<AppDbContext> options)
            : base(options)
        {
        }

        public DbSet<Role> Roles { get; set; }
        public DbSet<User> Users { get; set; }
        public DbSet<Course> Courses { get; set; }
        public DbSet<Assignment> Assignments { get; set; }
        public DbSet<Submission> Submissions { get; set; }

        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            base.OnModelCreating(modelBuilder);

            // ==================================================
            // PRIMARY KEYS
            // ==================================================

            modelBuilder.Entity<Role>()
                .HasKey(r => r.RoleId);

            modelBuilder.Entity<User>()
                .HasKey(u => u.UserId);

            modelBuilder.Entity<Course>()
                .HasKey(c => c.CourseId);

            modelBuilder.Entity<Assignment>()
                .HasKey(a => a.AssignmentId);

            modelBuilder.Entity<Submission>()
                .HasKey(s => s.SubmissionId);

            // ==================================================
            // ROLE AND USER
            // ==================================================

            modelBuilder.Entity<User>()
                .HasOne(u => u.Role)
                .WithMany(r => r.Users)
                .HasForeignKey(u => u.RoleId)
                .OnDelete(DeleteBehavior.NoAction);

            // ==================================================
            // COURSE AND FACULTY
            // ==================================================

            modelBuilder.Entity<Course>()
                .HasOne(c => c.Faculty)
                .WithMany(u => u.Courses)
                .HasForeignKey(c => c.FacultyId)
                .OnDelete(DeleteBehavior.NoAction);

            modelBuilder.Entity<Course>()
                .HasOne(c => c.CreatedByUser)
                .WithMany()
                .HasForeignKey(c => c.CreatedBy)
                .OnDelete(DeleteBehavior.NoAction);

            modelBuilder.Entity<Course>()
                .HasOne(c => c.UpdatedByUser)
                .WithMany()
                .HasForeignKey(c => c.UpdatedBy)
                .OnDelete(DeleteBehavior.NoAction);

            // ==================================================
            // COURSE AND ASSIGNMENT
            // ==================================================

            modelBuilder.Entity<Assignment>()
                .HasOne(a => a.Course)
                .WithMany(c => c.Assignments)
                .HasForeignKey(a => a.CourseId)
                .OnDelete(DeleteBehavior.NoAction);

            modelBuilder.Entity<Assignment>()
                .HasOne(a => a.CreatedByUser)
                .WithMany()
                .HasForeignKey(a => a.CreatedBy)
                .OnDelete(DeleteBehavior.NoAction);

            modelBuilder.Entity<Assignment>()
                .HasOne(a => a.UpdatedByUser)
                .WithMany()
                .HasForeignKey(a => a.UpdatedBy)
                .OnDelete(DeleteBehavior.NoAction);

            // ==================================================
            // ASSIGNMENT AND SUBMISSION
            // ==================================================

            modelBuilder.Entity<Submission>()
                .HasOne(s => s.Assignment)
                .WithMany(a => a.Submissions)
                .HasForeignKey(s => s.AssignmentId)
                .OnDelete(DeleteBehavior.NoAction);

            modelBuilder.Entity<Submission>()
                .HasOne(s => s.Student)
                .WithMany(u => u.Submissions)
                .HasForeignKey(s => s.StudentId)
                .OnDelete(DeleteBehavior.NoAction);

            modelBuilder.Entity<Submission>()
                .HasOne(s => s.GradedByUser)
                .WithMany()
                .HasForeignKey(s => s.GradedBy)
                .OnDelete(DeleteBehavior.NoAction);
        }
    }
}
```

### Primary Key Explanation

| Entity | Primary key |
|---|---|
| `Role` | `RoleId` |
| `User` | `UserId` |
| `Course` | `CourseId` |
| `Assignment` | `AssignmentId` |
| `Submission` | `SubmissionId` |

---

## 4. Register the DbContext

Update `Program.cs`.

### Program.cs

```csharp
using lab6.Data;
using Microsoft.EntityFrameworkCore;
using Scalar.AspNetCore;

var builder = WebApplication.CreateBuilder(args);

builder.Services.AddControllers();

builder.Services.AddDbContext<AppDbContext>(options =>
    options.UseSqlServer(
        builder.Configuration.GetConnectionString("DefaultConnection")));

builder.Services.AddOpenApi();

var app = builder.Build();

if (app.Environment.IsDevelopment())
{
    app.MapOpenApi();
    app.MapScalarApiReference();
}

app.UseHttpsRedirection();

app.UseAuthorization();

app.MapControllers();

app.Run();
```

### appsettings.json

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft.AspNetCore": "Warning"
    }
  },
  "AllowedHosts": "*",
  "ConnectionStrings": {
    "DefaultConnection": "Server=your server;Database=CourseHubDB;Trusted_Connection=True;TrustServerCertificate=True;"
  }
}
```

The SQL Server provider is configured using:

```csharp
options.UseSqlServer(
    builder.Configuration.GetConnectionString("DefaultConnection"));
```

ASP.NET Core reads the connection string from `appsettings.json` and makes `AppDbContext` available through dependency injection.

---

## 5. Relationship Summary

| Relationship | Foreign key | Delete behavior |
|---|---|---|
| User → Role | `User.RoleId` | `NoAction` |
| Course → Faculty | `Course.FacultyId` | `NoAction` |
| Course → Creator | `Course.CreatedBy` | `NoAction` |
| Course → Updater | `Course.UpdatedBy` | `NoAction` |
| Assignment → Course | `Assignment.CourseId` | `NoAction` |
| Assignment → Creator | `Assignment.CreatedBy` | `NoAction` |
| Assignment → Updater | `Assignment.UpdatedBy` | `NoAction` |
| Submission → Assignment | `Submission.AssignmentId` | `NoAction` |
| Submission → Student | `Submission.StudentId` | `NoAction` |
| Submission → Grader | `Submission.GradedBy` | `NoAction` |

---

## 6. Relationship Explanation

### One-to-Many: Role and User

A role can have many users:

```csharp
modelBuilder.Entity<User>()
    .HasOne(u => u.Role)
    .WithMany(r => r.Users)
    .HasForeignKey(u => u.RoleId)
    .OnDelete(DeleteBehavior.NoAction);
```

### One-to-Many: Course and Assignment

A course can have many assignments:

```csharp
modelBuilder.Entity<Assignment>()
    .HasOne(a => a.Course)
    .WithMany(c => c.Assignments)
    .HasForeignKey(a => a.CourseId)
    .OnDelete(DeleteBehavior.NoAction);
```

### One-to-Many: Assignment and Submission

An assignment can have many submissions:

```csharp
modelBuilder.Entity<Submission>()
    .HasOne(s => s.Assignment)
    .WithMany(a => a.Submissions)
    .HasForeignKey(s => s.AssignmentId)
    .OnDelete(DeleteBehavior.NoAction);
```

### Multiple Relationships Between the Same Entities

For example, a `Course` can be associated with a `User` as faculty, creator, or updater. Each relationship is configured separately using its own foreign-key property:

- `FacultyId`
- `CreatedBy`
- `UpdatedBy`

Similarly, a `Submission` can refer to a student and an optional grading faculty member.

---

## 7. Questions

1. What is an EF Core entity?
2. What is a primary key?
3. What is a foreign key?
4. What is a navigation property?
5. What is the purpose of `DbSet<T>`?
6. What is the purpose of `DbContext`?
7. How are primary keys configured manually?
8. How are foreign keys configured manually?
9. What does `OnModelCreating` do?
10. What does `DeleteBehavior.NoAction` do?
11. What is the purpose of `AddDbContext`?
12. How is the SQL Server connection string read in `Program.cs`?

---

## 8. Answers

1. An EF Core entity is a C# class that represents a database table.
2. A primary key uniquely identifies each record in a table.
3. A foreign key references the primary key of another table.
4. A navigation property provides access to related entities.
5. `DbSet<T>` exposes an entity set for querying and modifying records.
6. `DbContext` represents a session used to interact with the database.
7. Primary keys are configured using `HasKey` inside `OnModelCreating`.

   ```csharp
   modelBuilder.Entity<Course>()
       .HasKey(c => c.CourseId);
   ```

8. Foreign keys are configured using `HasOne`, `WithMany`, and `HasForeignKey`.

   ```csharp
   modelBuilder.Entity<Course>()
       .HasOne(c => c.Faculty)
       .WithMany(u => u.Courses)
       .HasForeignKey(c => c.FacultyId)
       .OnDelete(DeleteBehavior.NoAction);
   ```

9. `OnModelCreating` defines EF Core mappings and relationships.
10. `DeleteBehavior.NoAction` prevents the database from automatically deleting dependent records.
11. `AddDbContext` registers the context with ASP.NET Core dependency injection.
12. The connection string is read using:

    ```csharp
    builder.Configuration.GetConnectionString("DefaultConnection")
    ```

---

## 9. Create the Database Migration

Open Package Manager Console and run:

```powershell
Add-Migration InitialCreate
Update-Database
```

Or use the .NET CLI:

```bash
dotnet ef migrations add InitialCreate
dotnet ef database update
```

### Verify the Database

Check SQL Server for these tables:

- `Roles`
- `Users`
- `Courses`
- `Assignments`
- `Submissions`

Verify that primary keys and foreign-key relationships are created correctly.

---

## 10. Final Result

The application now contains:

- Five EF Core entities.
- One `DbContext`.
- SQL Server database registration.
- Manually configured primary keys.
- Manually configured foreign keys.
- One-to-many navigation properties.
- `DeleteBehavior.NoAction` on relationships.
- A connection string in `appsettings.json`.

> **Important:** `Course.CreatedBy` and `Course.UpdatedBy` are nullable in the provided model. `Submission.GradedBy` is also nullable. `Assignment.CreatedBy` is non-nullable, so every assignment must have a creator.
