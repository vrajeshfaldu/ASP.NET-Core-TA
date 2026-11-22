# Lab 8: EF Core Seed Data and Migrations

## Learning objectives

By the end of this lab, you will be able to:

1. Seed roles, users, and courses with EF Core `HasData`.
2. Create and apply a migration containing seed data.
3. Roll back to the first migration, remove and recreate the seed migration, and apply it again.
4. See what happens when seed data violates a unique email index.

## 1. Add seed data with `HasData`

In `AppDbContext`, add the following inside `OnModelCreating`, after your entity and relationship configuration. The primary key and foreign key values are assigned manually so EF Core can include them in the migration.

```csharp
// Roles: 1 = Admin, 2 = Faculty, 3 = Student
modelBuilder.Entity<Role>().HasData(
    new Role
    {
        RoleId = 1,
        RoleName = "Admin"
    },
    new Role
    {
        RoleId = 2,
        RoleName = "Faculty"
    },
    new Role
    {
        RoleId = 3,
        RoleName = "Student"
    }
);

// Users: RoleId is a foreign key to Role.RoleId.
modelBuilder.Entity<User>().HasData(
    new User
    {
        UserId = 1,
        RoleId = 1,
        FullName = "Admin User",
        Email = "admin@coursehub.com",
        PasswordHash = "Admin@123",
        IsActive = true,
        IsDeleted = false
    },
    new User
    {
        UserId = 2,
        RoleId = 2,
        FullName = "Faculty User",
        Email = "faculty@coursehub.com",
        PasswordHash = "Faculty@123",
        IsActive = true,
        IsDeleted = false
    },
    new User
    {
        UserId = 3,
        RoleId = 3,
        FullName = "Student One",
        Email = "student1@coursehub.com",
        PasswordHash = "Student@123",
        IsActive = true,
        IsDeleted = false
    },
    new User
    {
        UserId = 4,
        RoleId = 3,
        FullName = "Student Two",
        Email = "student2@coursehub.com",
        PasswordHash = "Student@123",
        IsActive = true,
        IsDeleted = false
    }
);

// Courses: FacultyId is a foreign key to Users.UserId.
modelBuilder.Entity<Course>().HasData(
    new Course
    {
        CourseId = 1,
        CourseCode = "CS101",
        CourseTitle = "C# Programming",
        FacultyId = 2,
        Credits = 3,
        IsDeleted = false
    },
    new Course
    {
        CourseId = 2,
        CourseCode = "CS102",
        CourseTitle = "ASP.NET Core",
        FacultyId = 2,
        Credits = 3,
        IsDeleted = false
    }
);
```

> **Security note:** The password values above are for a classroom demonstration only. Real applications must store securely hashed passwords, never plaintext passwords.

## 2. Create and apply the `SeedData` migration

Run these commands from Visual Studio’s **Package Manager Console**:

```powershell
Add-Migration SeedData
Update-Database
```

Or run these commands from the project directory with the .NET CLI:

```bash
dotnet ef migrations add SeedData
dotnet ef database update
```

EF Core creates a migration containing the seed inserts. `Update-Database` applies the migration to the database.

## 3. Roll back, remove, recreate, and re-apply the migration

Replace `InitialCreate` below with the name of your first migration if it is different.

First, roll the database back to the first migration:

```powershell
Update-Database InitialCreate
```

Then remove the now-unapplied `SeedData` migration:

```powershell
Remove-Migration
```

Recreate and apply it:

```powershell
Add-Migration SeedData
Update-Database
```

The equivalent .NET CLI commands are:

```bash
dotnet ef database update InitialCreate
dotnet ef migrations remove
dotnet ef migrations add SeedData
dotnet ef database update
```

> **Important:** `Remove-Migration` removes the latest migration from the project; it does not undo a migration that is still applied to the database. Roll back to the earlier migration first. If you only want to roll back and then re-apply the existing migration, skip `Remove-Migration` and run `Update-Database SeedData` instead.

## 4. Test the unique email constraint

The `User.Email` unique index should reject duplicate email addresses. To test it, temporarily add a fifth seeded user with an email already used by another user:

```csharp
new User
{
    UserId = 5,
    RoleId = 3,
    FullName = "Duplicate Student",
    Email = "student1@coursehub.com",
    PasswordHash = "Student@123",
    IsActive = true,
    IsDeleted = false
}
```

Then create and apply a migration:

```powershell
Add-Migration DuplicateEmailSeed
Update-Database
```

Because `student1@coursehub.com` is already present and `Email` has a unique index, applying the migration should fail with a database unique-constraint error. The exact error text depends on the database provider; with SQL Server, it commonly reports a duplicate key for the `Users` email index.

After observing the error:

1. Remove the duplicate user from `HasData`.
2. Remove the failed, unapplied migration:

   ```powershell
   Remove-Migration
   ```

3. Create a corrected migration if needed, then apply it:

   ```powershell
   Add-Migration SeedData
   Update-Database
   ```

## Expected result

After the `SeedData` migration is successfully applied, the database contains:

- Three roles: Admin, Faculty, and Student.
- Four users: one Admin, one Faculty, and two Students.
- Two courses assigned to the Faculty user.
- No duplicate user email addresses.
