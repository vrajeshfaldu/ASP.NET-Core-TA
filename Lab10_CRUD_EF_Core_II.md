# Lab 10 -- CRUD Using EF Core -- II

## CourseHub -- ASP.NET Core Web API

## 1. Lab Overview

Lab 9 covered basic EF Core CRUD: `GetAll()`, `GetById()`, `Post()`,
`UpdateById()` and `DeleteById()`.

Lab 10 adds advanced functionality required by the Darshan University
lab manual:

1.  Soft delete
2.  Active/inactive user
3.  User registration
4.  Login
5.  Logout
6.  Assignment evaluation
7.  Change submission status
8.  When a faculty creates an assignment, automatically create a
    submission record for every registered student with status
    `Pending`.

------------------------------------------------------------------------

## 2. Important Concepts

### EF Core

Entity Framework Core is an ORM for .NET. It lets us work with database
tables through C# entities and `DbContext`.

``` csharp
var users = await _context.Users.ToListAsync();
```

### DbContext

`DbContext` is the bridge between the application and database.

``` csharp
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
}
```

### Dependency Injection

The controller receives `CourseHubDbContext` through its constructor:

``` csharp
private readonly CourseHubDbContext _context;

public UsersController(CourseHubDbContext context)
{
    _context = context;
}
```

### Soft Delete

Normal delete removes a row. Soft delete keeps the row and changes a
flag:

``` csharp
user.IsDeleted = true;
```

### Active / Inactive

``` text
IsActive = true  → active user
IsActive = false → inactive user
```

### Submission Status

For Lab 10, the implementation uses:

``` text
Pending
Submitted
Graded
Late
```

`Pending` means the student has not submitted the assignment yet.

------------------------------------------------------------------------

# Question 1 -- Add Advanced Functionality

## Q1(A) Soft Delete

### Concept

Soft delete does not physically remove a user. It sets `IsDeleted` to
`true`.

### Add to `UsersController`

``` csharp
// Soft deletes a user by setting IsDeleted to true
[HttpDelete("soft/{id}")]
public async Task<IActionResult> SoftDelete(int id)
{
    var user = await _context.Users.FindAsync(id);

    if (user == null)
        return NotFound();

    user.IsDeleted = true;

    await _context.SaveChangesAsync();

    return Ok(user);
}
```

### Explanation

-   `FindAsync(id)` finds the user.
-   `IsDeleted = true` performs the soft delete.
-   `SaveChangesAsync()` saves the change.
-   `NotFound()` returns 404 if the user does not exist.

------------------------------------------------------------------------

## Q1(B) Active / Inactive User

### Concept

`IsActive` controls whether the user is active.

### Add to `UsersController`

``` csharp
// Changes the user's active or inactive status
[HttpPut("active/{id}")]
public async Task<IActionResult> ChangeActiveStatus(int id, bool isActive)
{
    var user = await _context.Users.FindAsync(id);

    if (user == null)
        return NotFound();

    user.IsActive = isActive;

    await _context.SaveChangesAsync();

    return Ok(user);
}
```

Example:

``` text
PUT /api/Users/active/5?isActive=false
```

------------------------------------------------------------------------

## Q1(C) User Registration

### Concept

Registration creates a new user. First check whether the email already
exists.

### Add to `UsersController`

``` csharp
// Registers a new user and checks whether the email already exists
[HttpPost("register")]
public async Task<IActionResult> Register(User user)
{
    var existingUser = await _context.Users
        .FirstOrDefaultAsync(u => u.Email == user.Email);

    if (existingUser != null)
        return Conflict("Email already exists.");

    user.IsActive = true;
    user.IsDeleted = false;

    _context.Users.Add(user);

    await _context.SaveChangesAsync();

    return StatusCode(201, user);
}
```

### Important points

``` csharp
FirstOrDefaultAsync()
```

checks for an existing email.

``` csharp
Conflict()
```

returns HTTP 409.

``` csharp
StatusCode(201, user)
```

returns HTTP 201 after successful registration.

> For this lab implementation, the existing password field is used
> directly. A real production application should use secure password
> hashing.

------------------------------------------------------------------------

## Q1(D) Login

### Concept

Login checks email and password and also prevents deleted or inactive
users from logging in.

### Add to `UsersController`

``` csharp
// Checks the user's email and password and performs login
[HttpPost("login")]
public async Task<IActionResult> Login(string email, string password)
{
    var user = await _context.Users
        .FirstOrDefaultAsync(u =>
            u.Email == email &&
            u.PasswordHash == password &&
            u.IsDeleted == false);

    if (user == null)
        return Unauthorized("Invalid email or password.");

    if (!user.IsActive)
        return Unauthorized("User is inactive.");

    return Ok(user);
}
```

### Flow

``` text
Email + Password
       ↓
Find user
       ↓
Check IsDeleted
       ↓
Check IsActive
       ↓
200 OK / 401 Unauthorized
```

------------------------------------------------------------------------

## Q1(E) Logout

### Concept

For the simple lab implementation, logout returns a success message.

### Add to `UsersController`

``` csharp
// Performs logout and returns a logout success message
[HttpPost("logout")]
public IActionResult Logout()
{
    return Ok("Logout successful.");
}
```

In a real JWT-based system, logout normally requires token/session
handling.

------------------------------------------------------------------------

## Q1(F) Assignment Evaluation

### Concept

When faculty evaluates a submission, update:

-   `MarksObtained`
-   `Remarks`
-   `Status`
-   `GradedAt`

### Add to `SubmissionsController`

``` csharp
// Evaluates a student's assignment submission by adding marks and remarks
[HttpPut("evaluate/{id}")]
public async Task<IActionResult> Evaluate(
    int id,
    int marks,
    string remarks)
{
    var submission = await _context.Submissions.FindAsync(id);

    if (submission == null)
        return NotFound();

    submission.MarksObtained = marks;
    submission.Remarks = remarks;
    submission.Status = "Graded";
    submission.GradedAt = DateTime.UtcNow;

    await _context.SaveChangesAsync();

    return Ok(submission);
}
```

### Main idea

``` text
Faculty evaluates
      ↓
Marks + Remarks
      ↓
Status = Graded
      ↓
GradedAt = current UTC time
      ↓
SaveChangesAsync()
```

------------------------------------------------------------------------

## Q1(G) Change Submission Status

### Concept

Only allowed statuses should be stored.

### Add to `SubmissionsController`

``` csharp
// Changes the status of a student's assignment submission
[HttpPut("status/{id}")]
public async Task<IActionResult> ChangeStatus(int id, string status)
{
    var validStatuses = new[]
    {
        "Pending",
        "Submitted",
        "Graded",
        "Late"
    };

    if (!validStatuses.Contains(status))
        return BadRequest("Invalid submission status.");

    var submission = await _context.Submissions.FindAsync(id);

    if (submission == null)
        return NotFound();

    submission.Status = status;

    await _context.SaveChangesAsync();

    return Ok(submission);
}
```

### Why validate status?

Without validation, someone could send:

``` text
Status = "ABC"
```

Validation prevents invalid values.

------------------------------------------------------------------------

# Question 2 -- Create Pending Submissions Automatically

## Requirement

When faculty creates/uploads an assignment, all registered students
should automatically get a record in the `Submissions` table.

Initial status:

``` text
Pending
```

### Where to add?

Modify the `Post()` method in `AssignmentsController`.

``` csharp
// Creates an assignment and automatically creates pending submissions for all active students
[HttpPost]
public async Task<IActionResult> Post(Assignment assignment)
{
    _context.Assignments.Add(assignment);

    await _context.SaveChangesAsync();

    // Get all active and non-deleted students
    var students = await _context.Users
        .Where(u =>
            u.RoleId == 3 &&
            u.IsActive == true &&
            u.IsDeleted == false)
        .ToListAsync();

    // Create a pending submission for every student
    foreach (var student in students)
    {
        var submission = new Submission
        {
            AssignmentId = assignment.AssignmentId,
            StudentId = student.UserId,
            Status = "Pending",
            IsDeleted = false
        };

        _context.Submissions.Add(submission);
    }

    await _context.SaveChangesAsync();

    return StatusCode(201, assignment);
}
```

## Explanation

### Step 1 -- Save assignment

``` csharp
_context.Assignments.Add(assignment);
await _context.SaveChangesAsync();
```

We save first because EF Core generates the `AssignmentId`.

### Step 2 -- Find students

``` csharp
var students = await _context.Users
    .Where(u =>
        u.RoleId == 3 &&
        u.IsActive == true &&
        u.IsDeleted == false)
    .ToListAsync();
```

This selects active, non-deleted students. In the seeded project data,
`RoleId = 3` represents Student.

### Step 3 -- Create one submission per student

``` csharp
foreach (var student in students)
```

The loop runs once for every student.

### Step 4 -- Set Pending

``` csharp
Status = "Pending"
```

### Step 5 -- Save all submissions

``` csharp
await _context.SaveChangesAsync();
```

------------------------------------------------------------------------

# 3. Update Submission Default Configuration

The original configuration may contain:

``` csharp
modelBuilder.Entity<Submission>()
    .Property(s => s.Status)
    .HasDefaultValue("Submitted");
```

For Lab 10's pending-submission requirement, change it to:

``` csharp
// Sets Pending as the default submission status
modelBuilder.Entity<Submission>()
    .Property(s => s.Status)
    .HasDefaultValue("Pending");
```

------------------------------------------------------------------------

# 4. Update Submission Check Constraint

If the existing constraint is:

``` csharp
modelBuilder.Entity<Submission>()
    .ToTable(t => t.HasCheckConstraint(
        "CK_Submission_Status",
        "Status IN ('Submitted', 'Graded', 'Late')"));
```

change it to:

``` csharp
// Allows Pending, Submitted, Graded and Late statuses
modelBuilder.Entity<Submission>()
    .ToTable(t => t.HasCheckConstraint(
        "CK_Submission_Status",
        "Status IN ('Pending', 'Submitted', 'Graded', 'Late')"));
```

Otherwise SQL Server may reject `Pending`.

------------------------------------------------------------------------

# 5. Create Migration

After changing the model:

``` powershell
Add-Migration AddSubmissionPendingStatus
```

Then apply it:

``` powershell
Update-Database
```

Flow:

``` text
Change model
    ↓
Add-Migration
    ↓
Migration generated
    ↓
Update-Database
    ↓
Database updated
```

------------------------------------------------------------------------

# 6. Important FilePath Note

The original CourseHub schema has a required `FilePath` for
`Submission`.

Question 2 creates a pending submission before the student uploads a
file. Therefore, if `FilePath` is required/non-nullable in your
model/database, the automatic insert may fail.

Handle this according to your instructor's expected schema, for example
by allowing `FilePath` to be nullable for pending records if your
project permits that change.

Do not silently change the original schema if your instructor expects it
unchanged.

------------------------------------------------------------------------

# 7. Controller Placement Summary

``` text
Controllers/
├── RolesController.cs
├── UsersController.cs
├── CoursesController.cs
├── AssignmentsController.cs
└── SubmissionsController.cs
```

### UsersController

``` text
SoftDelete()
ChangeActiveStatus()
Register()
Login()
Logout()
```

### AssignmentsController

``` text
Post()
```

Modify `Post()` for Question 2.

### SubmissionsController

``` text
Evaluate()
ChangeStatus()
```

------------------------------------------------------------------------

# 8. HTTP Status Codes

  Operation                     Status
  ----------------------------- ------------------
  Successful GET                200 OK
  Successful POST               201 Created
  Successful PUT                200 OK
  Successful DELETE             204 No Content
  Not found                     404 Not Found
  Invalid input/status          400 Bad Request
  Duplicate email               409 Conflict
  Invalid login/inactive user   401 Unauthorized

------------------------------------------------------------------------

# 9. Complete Lab 10 Flow

## Registration

``` text
POST /register
      ↓
Check email
      ↓
Create user
      ↓
201 Created
```

## Login

``` text
POST /login
      ↓
Check email/password
      ↓
Check IsDeleted
      ↓
Check IsActive
      ↓
200 OK or 401
```

## Soft Delete

``` text
DELETE /soft/{id}
      ↓
Find user
      ↓
IsDeleted = true
      ↓
Save
      ↓
200 OK
```

## Assignment Creation

``` text
Faculty creates assignment
          ↓
Save assignment
          ↓
Find active students
          ↓
Create Submission for each
          ↓
Status = Pending
          ↓
Save submissions
```

## Evaluation

``` text
Faculty evaluates
      ↓
MarksObtained
Remarks
Status = Graded
GradedAt
      ↓
Save
```

------------------------------------------------------------------------

# 10. Quick Revision

``` text
SOFT DELETE
user.IsDeleted = true;

ACTIVE
user.IsActive = true;

INACTIVE
user.IsActive = false;

REGISTER
Check duplicate email
Add user
SaveChangesAsync()

LOGIN
Check email + password
Check IsDeleted
Check IsActive

LOGOUT
Return success message

EVALUATION
MarksObtained = marks
Remarks = remarks
Status = "Graded"
GradedAt = DateTime.UtcNow

CHANGE STATUS
Pending / Submitted / Graded / Late

AUTO PENDING SUBMISSIONS
Create Assignment
      ↓
Find students
      ↓
foreach student
      ↓
Create Submission
Status = Pending
```

------------------------------------------------------------------------

# 11. Viva Questions

### 1. What is soft delete?

Soft delete keeps the record in the database and sets
`IsDeleted = true`.

### 2. Why use soft delete?

It preserves the original database record.

### 3. What is `IsActive`?

It tells whether a user is active or inactive.

### 4. Difference between `IsDeleted` and `IsActive`?

`IsDeleted` indicates logical deletion. `IsActive` indicates whether the
user is currently active.

### 5. Why check duplicate email?

To prevent two users from registering with the same email.

### 6. What is HTTP 409?

`409 Conflict` indicates a conflict such as an already existing email.

### 7. What is HTTP 401?

`401 Unauthorized` indicates that authentication is not valid.

### 8. Why use `SaveChangesAsync()`?

It saves EF Core changes to the database.

### 9. Why save the assignment before creating submissions?

Because the generated `AssignmentId` is required by each submission.

### 10. Why create a submission for every student?

To track each student's submission status.

### 11. What is the initial status?

`Pending`.

### 12. What happens during evaluation?

Marks and remarks are stored, status becomes `Graded`, and `GradedAt` is
set.

### 13. Why validate submission status?

To prevent invalid status values.

### 14. What are the valid statuses?

``` text
Pending
Submitted
Graded
Late
```

### 15. What is Dependency Injection?

It provides required objects such as `CourseHubDbContext` to a class
through its constructor.

------------------------------------------------------------------------

# 12. Exam Memory Sheet

``` text
LAB 10 = ADVANCED EF CORE FUNCTIONALITY

1. Soft Delete
   IsDeleted = true

2. Active/Inactive
   IsActive = true / false

3. Registration
   Check email → Add → Save → 201

4. Login
   Email + Password
   IsDeleted check
   IsActive check
   200 / 401

5. Logout
   Simple success response

6. Evaluation
   Marks + Remarks
   Status = Graded
   GradedAt

7. Change Status
   Pending / Submitted / Graded / Late

8. Faculty creates assignment
   Save Assignment
   Find active students
   Create Submission for every student
   Status = Pending
   Save

9. Migration
   Add-Migration AddSubmissionPendingStatus
   Update-Database
```

------------------------------------------------------------------------

## Source

Based on the **Darshan University -- 2401CS521 Lab Manual**, Lab 10:
**CRUD Using EF Core -- II**.

The source requirements are:

-   **A(1):** Add soft delete, active-inactive user, registration,
    login, logout, assignment evaluation, submission status, etc.
-   **B(2):** When faculty uploads an assignment, automatically enter
    registered students into the Submission table with default
    submission status as pending.
