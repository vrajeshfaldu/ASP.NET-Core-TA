# Lab 6: EF Core Indexes, Defaults, Constraints, and Migrations

## Concepts to know first

- **Unique index:** Prevents duplicate values in a column. For example, two users cannot have the same email address.
- **Composite unique index:** Applies uniqueness to a combination of columns. A student can submit an assignment only once when `(AssignmentId, StudentId)` is unique.
- **Default value:** A value the database uses when an insert does not provide one for that column.
- **Check constraint:** Makes the database reject rows that do not meet a condition, such as a course having between 1 and 6 credits.
- **Migration:** A versioned set of database changes generated from your EF Core model. `Update-Database` applies those changes to the database.

## Configure indexes, defaults, and constraints

Add these configurations inside `OnModelCreating`, after `base.OnModelCreating(modelBuilder)`. Keep your existing primary-key and relationship configurations. If you already have any of these defaults or constraints, replace the existing configuration rather than adding a duplicate.

```csharp
// Unique indexes
modelBuilder.Entity<Role>()
    .HasIndex(r => r.RoleName)
    .IsUnique();

modelBuilder.Entity<User>()
    .HasIndex(u => u.Email)
    .IsUnique();

modelBuilder.Entity<Course>()
    .HasIndex(c => c.CourseCode)
    .IsUnique();

// One submission per assignment per student
modelBuilder.Entity<Submission>()
    .HasIndex(s => new { s.AssignmentId, s.StudentId })
    .IsUnique();

// User defaults
modelBuilder.Entity<User>()
    .Property(u => u.IsActive)
    .HasDefaultValue(true);

modelBuilder.Entity<User>()
    .Property(u => u.IsDeleted)
    .HasDefaultValue(false);

modelBuilder.Entity<User>()
    .Property(u => u.CreatedAt)
    .HasDefaultValueSql("GETUTCDATE()");

// Course defaults
modelBuilder.Entity<Course>()
    .Property(c => c.Credits)
    .HasDefaultValue(3);

modelBuilder.Entity<Course>()
    .Property(c => c.IsDeleted)
    .HasDefaultValue(false);

modelBuilder.Entity<Course>()
    .Property(c => c.CreatedAt)
    .HasDefaultValueSql("GETUTCDATE()");

// Assignment defaults
modelBuilder.Entity<Assignment>()
    .Property(a => a.MaxMarks)
    .HasDefaultValue(100);

modelBuilder.Entity<Assignment>()
    .Property(a => a.IsDeleted)
    .HasDefaultValue(false);

modelBuilder.Entity<Assignment>()
    .Property(a => a.CreatedAt)
    .HasDefaultValueSql("GETUTCDATE()");

// Submission defaults
modelBuilder.Entity<Submission>()
    .Property(s => s.Status)
    .HasDefaultValue("Submitted");

modelBuilder.Entity<Submission>()
    .Property(s => s.IsDeleted)
    .HasDefaultValue(false);

modelBuilder.Entity<Submission>()
    .Property(s => s.CreatedAt)
    .HasDefaultValueSql("GETUTCDATE()");

// Check constraints
modelBuilder.Entity<Course>()
    .ToTable(t => t.HasCheckConstraint(
        "CK_Course_Credits",
        "Credits BETWEEN 1 AND 6"));

modelBuilder.Entity<Assignment>()
    .ToTable(t => t.HasCheckConstraint(
        "CK_Assignment_MaxMarks",
        "MaxMarks > 0"));

modelBuilder.Entity<Submission>()
    .ToTable(t => t.HasCheckConstraint(
        "CK_Submission_Status",
        "Status IN ('Submitted', 'Graded', 'Late')"));
```

> **Status note:** These requirements set the default status to `"Submitted"` and allow only `"Submitted"`, `"Graded"`, or `"Late"`. Ensure your application does not try to save another status, such as `"Pending"`.

`GETUTCDATE()` is a SQL Server function. It supplies the current UTC time when the database inserts a row without a `CreatedAt` value.

## Create and apply the migration

In Visual Studio’s **Package Manager Console**, run:

```powershell
Add-Migration InitialCreate
Update-Database
```

Or use the .NET CLI from the project directory:

```bash
dotnet ef migrations add InitialCreate
dotnet ef database update
```

Make sure the project has the EF Core SQL Server provider and EF Core tools installed, and that your connection string points to the intended SQL Server database.

## Questions and answers

### 1. What is an EF Core entity?

An EF Core entity is a C# class that EF Core maps to a database table.

### 2. What is a primary key?

A primary key uniquely identifies each record in a table.

### 3. What is a foreign key?

A foreign key refers to a key in another table and connects related records.

### 4. What is a navigation property?

A navigation property provides access to related entities, such as `Course.Faculty`.

### 5. What is the purpose of `DbSet<T>`?

`DbSet<T>` represents an entity set that can be queried and changed through the `DbContext`, such as `DbSet<Course>`.

### 6. What is the purpose of `DbContext`?

`DbContext` manages the application’s session with the database, including querying and saving entities.

### 7. How are primary keys configured manually?

Use `HasKey` inside `OnModelCreating`. For example:

```csharp
modelBuilder.Entity<Course>()
    .HasKey(c => c.CourseId);
```

### 8. How are foreign keys configured manually?

Use relationship methods such as `HasOne`, `WithMany`, and `HasForeignKey`.

### 9. What does `OnModelCreating` do?

`OnModelCreating` configures the EF Core model, including keys, indexes, relationships, defaults, and constraints.

### 10. What does `DeleteBehavior.NoAction` do?

It tells the database not to automatically delete related records when a referenced record is deleted. The database may reject the deletion if dependent records still exist.

### 11. What is the purpose of `AddDbContext`?

`AddDbContext` registers the context with ASP.NET Core dependency injection so it can be provided to controllers and other services.

### 12. How is the SQL Server connection string read in `Program.cs`?

Use `builder.Configuration.GetConnectionString("DefaultConnection")`, typically passed to `options.UseSqlServer(...)`.
