# Lab: EF Core Entities, DbContext, and Relationships

## Concepts to understand before the questions

### Entities and tables

In Entity Framework Core (EF Core), an **entity** is a C# class that represents data the application works with. A `DbSet<TEntity>` on a context makes that entity available to EF Core and typically corresponds to a database table. For this Course Hub example, the entities are `Role`, `User`, `Course`, `Assignment`, and `Submission`.

### Primary keys

A **primary key (PK)** uniquely identifies a row in a table. EF Core can infer a key from names such as `Id` or `<EntityName>Id`, but explicitly configuring keys with `HasKey` makes the mapping clear and is useful for learning and nonstandard key names.

### Foreign keys and navigation properties

A **foreign key (FK)** stores the key of a related row. For example, `User.RoleId` refers to `Role.RoleId`. A **navigation property** lets application code navigate between related entities: `User.Role` references one role, while `Role.Users` represents the users in that role. A collection navigation represents the “many” side of a one-to-many relationship.

The model below includes these relationships:

- One `Role` has many `User` records.
- One `User` (faculty) has many `Course` records.
- One `Course` has many `Assignment` records.
- One `Assignment` has many `Submission` records.
- One `User` (student) has many `Submission` records.
- Users may also be referenced as the creator/updater of courses and assignments, or as the grader of a submission.

### Delete behavior

EF Core uses delete behavior to decide what happens to dependent rows when a principal row is deleted. `DeleteBehavior.NoAction` prevents the database from automatically deleting dependent records. A delete that would violate a foreign-key constraint must be handled explicitly (for example, first reassign or remove dependent records). This is useful for records such as submissions that should not disappear automatically when a course or user is deleted.

### DbContext and dependency injection

A `DbContext` is EF Core’s unit of work: it exposes entity sets, configures the model, and tracks changes that can be persisted to the database. ASP.NET Core applications commonly register the context in dependency injection with `AddDbContext`. The provider (`UseSqlServer` here) and connection string tell EF Core which database to use.

---

## Questions

1. Create the five entities (`Role`, `User`, `Course`, `Assignment`, `Submission`) with navigation properties.
2. Create `CourseHubDbContext` and register it with a connection string.
3. Configure primary keys and foreign keys with appropriate delete behavior manually.

## Answers

### 1. Entity classes and navigation properties

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

        // Courses for which this user is the faculty member.
        public ICollection<Course> Courses { get; set; } = new List<Course>();

        // Assignments created by this user.
        public ICollection<Assignment> Assignments { get; set; } = new List<Assignment>();

        // Submissions made by this user as a student.
        public ICollection<Submission> Submissions { get; set; } = new List<Submission>();
    }
}
```

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

### 2–3. Context, keys, relationships, and delete behavior

The context below uses the entities above and configures each primary key and relationship explicitly. `NoAction` is applied to the foreign keys so that deleting a related role, user, course, or assignment does not cascade-delete dependent rows.

```csharp
using lab6.Models;
using Microsoft.EntityFrameworkCore;

namespace lab6.Data
{
    public class CourseHubDbContext : DbContext
    {
        public CourseHubDbContext(DbContextOptions<CourseHubDbContext> options)
            : base(options)
        {
        }

        public DbSet<User> Users { get; set; }
        public DbSet<Role> Roles { get; set; }
        public DbSet<Course> Courses { get; set; }
        public DbSet<Assignment> Assignments { get; set; }
        public DbSet<Submission> Submissions { get; set; }

        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            base.OnModelCreating(modelBuilder);

            // Primary keys
            modelBuilder.Entity<Role>().HasKey(r => r.RoleId);
            modelBuilder.Entity<User>().HasKey(u => u.UserId);
            modelBuilder.Entity<Course>().HasKey(c => c.CourseId);
            modelBuilder.Entity<Assignment>().HasKey(a => a.AssignmentId);
            modelBuilder.Entity<Submission>().HasKey(s => s.SubmissionId);

            // User -> Role: many users belong to one role.
            modelBuilder.Entity<User>()
                .HasOne(u => u.Role)
                .WithMany(r => r.Users)
                .HasForeignKey(u => u.RoleId)
                .OnDelete(DeleteBehavior.NoAction);

            // Course -> Faculty (User): one faculty member can teach many courses.
            modelBuilder.Entity<Course>()
                .HasOne(c => c.Faculty)
                .WithMany(u => u.Courses)
                .HasForeignKey(c => c.FacultyId)
                .OnDelete(DeleteBehavior.NoAction);

            // Course -> creator (User). This is a separate User relationship.
            modelBuilder.Entity<Course>()
                .HasOne(c => c.CreatedByUser)
                .WithMany()
                .HasForeignKey(c => c.CreatedBy)
                .OnDelete(DeleteBehavior.NoAction);

            // Course -> updater (User). This is another separate User relationship.
            modelBuilder.Entity<Course>()
                .HasOne(c => c.UpdatedByUser)
                .WithMany()
                .HasForeignKey(c => c.UpdatedBy)
                .OnDelete(DeleteBehavior.NoAction);

            // Assignment -> Course: one course can have many assignments.
            modelBuilder.Entity<Assignment>()
                .HasOne(a => a.Course)
                .WithMany(c => c.Assignments)
                .HasForeignKey(a => a.CourseId)
                .OnDelete(DeleteBehavior.NoAction);

            // Assignment -> creator (User). Use the matching inverse collection.
            modelBuilder.Entity<Assignment>()
                .HasOne(a => a.CreatedByUser)
                .WithMany(u => u.Assignments)
                .HasForeignKey(a => a.CreatedBy)
                .OnDelete(DeleteBehavior.NoAction);

            // Assignment -> updater (User).
            modelBuilder.Entity<Assignment>()
                .HasOne(a => a.UpdatedByUser)
                .WithMany()
                .HasForeignKey(a => a.UpdatedBy)
                .OnDelete(DeleteBehavior.NoAction);

            // Submission -> Assignment: one assignment can have many submissions.
            modelBuilder.Entity<Submission>()
                .HasOne(s => s.Assignment)
                .WithMany(a => a.Submissions)
                .HasForeignKey(s => s.AssignmentId)
                .OnDelete(DeleteBehavior.NoAction);

            // Submission -> Student (User): one student can make many submissions.
            modelBuilder.Entity<Submission>()
                .HasOne(s => s.Student)
                .WithMany(u => u.Submissions)
                .HasForeignKey(s => s.StudentId)
                .OnDelete(DeleteBehavior.NoAction);

            // Submission -> grader (User).
            modelBuilder.Entity<Submission>()
                .HasOne(s => s.GradedByUser)
                .WithMany()
                .HasForeignKey(s => s.GradedBy)
                .OnDelete(DeleteBehavior.NoAction);
        }
    }
}
```

> `Course.CreatedBy` is nullable, so its navigation is nullable too. In the supplied model, `Assignment.CreatedBy` is non-nullable; therefore every assignment must reference a creator. If assignments may be created without a recorded user, change that FK to `int?` and the navigation to `User?`.

### Register the context and connection string

In `Program.cs`, register the context with the SQL Server provider. ASP.NET Core will provide it to controllers through dependency injection.

```csharp
using lab6.Data;
using Microsoft.EntityFrameworkCore;

var builder = WebApplication.CreateBuilder(args);

builder.Services.AddControllers();
builder.Services.AddDbContext<CourseHubDbContext>(options =>
    options.UseSqlServer(
        builder.Configuration.GetConnectionString("DefaultConnection")));

var app = builder.Build();

app.UseHttpsRedirection();
app.UseAuthorization();
app.MapControllers();
app.Run();
```

Add a connection string to `appsettings.json`. Replace the server name with the SQL Server instance configured on your machine. Keep real credentials out of source control; use user secrets or environment variables where appropriate.

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=YOUR_SERVER;Database=CourseHubDB;Trusted_Connection=True;TrustServerCertificate=True;"
  }
}
```

### Relationship and key summary

| Entity | Primary key | Foreign keys and navigation |
|---|---|---|
| `Role` | `RoleId` | `Users` collection |
| `User` | `UserId` | `RoleId` → `Role`; `Courses`, `Assignments`, and `Submissions` collections for the configured relationships |
| `Course` | `CourseId` | `FacultyId`, `CreatedBy`, and `UpdatedBy` → `User` |
| `Assignment` | `AssignmentId` | `CourseId` → `Course`; `CreatedBy`, `UpdatedBy` → `User` |
| `Submission` | `SubmissionId` | `AssignmentId` → `Assignment`; `StudentId`, `GradedBy` → `User` |

`HasOne(...).WithMany(...)` configures a one-to-many relationship from the dependent entity's perspective. `HasForeignKey(...)` identifies the FK property, and `OnDelete(DeleteBehavior.NoAction)` configures the delete rule explicitly.
