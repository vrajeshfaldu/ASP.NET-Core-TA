# CourseHub Web API – CRUD Controllers

## Computer Science and Engineering

**Darshan Institute of Engineering & Technology**  
**Darshan University**

---

# 1. Introduction

In this lab, we create Web API controllers for the **CourseHub** application.

The CourseHub application contains the following modules:

1. Roles
2. Users
3. Courses
4. Assignments
5. Submissions

For each module, we will create API endpoints to:

- Get all records
- Get a single record by ID
- Add a new record
- Update an existing record
- Delete a record

These operations are called **CRUD operations**.

---

# 2. What is CRUD?

CRUD stands for:

| Operation | Meaning | HTTP Method |
|---|---|---|
| C | Create | POST |
| R | Read | GET |
| U | Update | PUT |
| D | Delete | DELETE |

### Create

Used to add a new record to the database.

```http
POST /api/Roles
```

### Read

Used to retrieve data from the database.

```http
GET /api/Roles
```

or

```http
GET /api/Roles/1
```

### Update

Used to modify an existing record.

```http
PUT /api/Roles/1
```

### Delete

Used to remove an existing record.

```http
DELETE /api/Roles/1
```

---

# 3. What is a Controller?

A **Controller** handles HTTP requests coming from the client.

```text
Client
   |
   | GET /api/Roles
   v
RolesController
   |
   v
CourseHubDbContext
   |
   v
Database
```

The controller receives the request, communicates with the database through `CourseHubDbContext`, and sends an HTTP response back to the client.

---

# 4. What is CourseHubDbContext?

`CourseHubDbContext` is the EF Core database context.

It provides access to database tables through `DbSet`.

Example:

```csharp
public DbSet<Role> Roles { get; set; }
public DbSet<User> Users { get; set; }
public DbSet<Course> Courses { get; set; }
public DbSet<Assignment> Assignments { get; set; }
public DbSet<Submission> Submissions { get; set; }
```

These `DbSet` properties represent database tables.

For example:

```csharp
_context.Roles
```

represents the `Roles` table.

```csharp
_context.Users
```

represents the `Users` table.

---

# 5. What is Dependency Injection?

**Dependency Injection (DI)** is a technique used to provide an object with the dependencies it needs.

Our controllers need `CourseHubDbContext` to communicate with the database.

Instead of creating the DbContext manually, ASP.NET Core provides it through the constructor.

Example:

```csharp
private readonly CourseHubDbContext _context;

public RolesController(CourseHubDbContext context)
{
    _context = context;
}
```

Here:

- `CourseHubDbContext` = dependency
- `context` = object received through constructor
- `_context` = private field used inside the controller

---

# 6. Why use Constructor Injection?

Constructor injection is useful because:

- It follows Dependency Injection principles.
- ASP.NET Core manages the DbContext lifetime.
- We don't need to manually create the database context.
- It makes controllers easier to test and maintain.

---

# 7. What is IActionResult?

`IActionResult` represents an HTTP response returned by a controller action.

Example:

```csharp
public async Task<IActionResult> GetAll()
```

The method can return different HTTP status codes.

For example:

```csharp
return Ok(data);
```

returns:

```text
200 OK
```

```csharp
return NotFound();
```

returns:

```text
404 Not Found
```

```csharp
return StatusCode(201, data);
```

returns:

```text
201 Created
```

```csharp
return NoContent();
```

returns:

```text
204 No Content
```

---

# 8. Important HTTP Status Codes

| Status Code | Meaning | Common Use |
|---|---|---|
| 200 | OK | Successful GET/UPDATE |
| 201 | Created | Successful POST |
| 204 | No Content | Successful DELETE |
| 400 | Bad Request | Invalid request |
| 404 | Not Found | Record doesn't exist |
| 500 | Internal Server Error | Server-side error |

For this lab, the main status codes are:

```text
200 OK
201 Created
204 No Content
404 Not Found
```

---

# 9. What is Entity Framework Core?

**Entity Framework Core (EF Core)** is an ORM.

ORM means:

> Object Relational Mapper

It allows us to work with database tables using C# classes and objects instead of writing SQL for every operation.

For example:

```csharp
var roles = await _context.Roles.ToListAsync();
```

EF Core converts this into a database query and returns the records.

---

# 10. Important EF Core Methods

## ToListAsync()

Used to retrieve all records.

```csharp
var roles = await _context.Roles.ToListAsync();
```

Meaning:

> Get all roles from the Roles table.

---

## FindAsync()

Used to find one record using its primary key.

```csharp
var role = await _context.Roles.FindAsync(id);
```

Meaning:

> Find the role whose primary key is equal to `id`.

---

## Add()

Used to add a new object.

```csharp
_context.Roles.Add(role);
```

---

## SaveChangesAsync()

Used to save changes to the database.

```csharp
await _context.SaveChangesAsync();
```

Without `SaveChangesAsync()`, the changes are not persisted to the database.

---

## Remove()

Used to delete an object.

```csharp
_context.Roles.Remove(role);
```

Then:

```csharp
await _context.SaveChangesAsync();
```

saves the deletion.

---

# 11. Why use async and await?

Database operations can take some time.

We use asynchronous methods such as:

```csharp
ToListAsync()
FindAsync()
SaveChangesAsync()
```

with:

```csharp
async
await
```

Example:

```csharp
public async Task<IActionResult> GetAll()
{
    var roles = await _context.Roles.ToListAsync();

    return Ok(roles);
}
```

This allows the application to handle database operations asynchronously.

---

# 12. Controller Route

We use:

```csharp
[ApiController]
[Route("api/[controller]")]
```

For example:

```csharp
public class RolesController : ControllerBase
```

The controller name is:

```text
RolesController
```

Therefore:

```text
[controller]
```

becomes:

```text
Roles
```

So the API route becomes:

```text
/api/Roles
```

---

# 13. HTTP Method Attributes

## GET

```csharp
[HttpGet]
```

Used to retrieve data.

## GET by ID

```csharp
[HttpGet("{id}")]
```

Used to retrieve one record using ID.

## POST

```csharp
[HttpPost]
```

Used to create a new record.

## PUT

```csharp
[HttpPut("{id}")]
```

Used to update a record.

## DELETE

```csharp
[HttpDelete("{id}")]
```

Used to delete a record.

---

# Question 1

## Create controllers for Roles, Users, Courses, Assignment and Submission modules.

We need to create five controllers:

```text
Controllers
│
├── RolesController.cs
├── UsersController.cs
├── CoursesController.cs
├── AssignmentsController.cs
└── SubmissionsController.cs
```

Each controller inherits from:

```csharp
ControllerBase
```

---

# Question 2

## Inject CourseHubDbContext via constructor using Dependency Injection.

The DbContext is injected into each controller.

General structure:

```csharp
private readonly CourseHubDbContext _context;

public ControllerName(CourseHubDbContext context)
{
    _context = context;
}
```

The `_context` object is then used to access the database.

---

# Question 3

## Create GetAll() and GetById() methods for Roles.

### RolesController.cs

```csharp
using lab6.Data;
using Microsoft.AspNetCore.Mvc;
using Microsoft.EntityFrameworkCore;

namespace lab6.Controllers
{
    [ApiController]
    [Route("api/[controller]")]
    public class RolesController : ControllerBase
    {
        private readonly CourseHubDbContext _context;

        public RolesController(CourseHubDbContext context)
        {
            _context = context;
        }

        // GET: api/Roles
        [HttpGet]
        public async Task<IActionResult> GetAll()
        {
            var roles = await _context.Roles.ToListAsync();

            return Ok(roles);
        }

        // GET: api/Roles/1
        [HttpGet("{id}")]
        public async Task<IActionResult> GetById(int id)
        {
            var role = await _context.Roles.FindAsync(id);

            if (role == null)
                return NotFound();

            return Ok(role);
        }
    }
}
```

### Explanation

`GetAll()` gets all roles using `ToListAsync()` and returns `200 OK`.

`GetById()` finds one role using `FindAsync(id)`. If it does not exist, it returns `404 Not Found`.

---

# Question 4

## Create GetAll() and GetById() methods for Users.

### UsersController.cs

```csharp
using lab6.Data;
using Microsoft.AspNetCore.Mvc;
using Microsoft.EntityFrameworkCore;

namespace lab6.Controllers
{
    [ApiController]
    [Route("api/[controller]")]
    public class UsersController : ControllerBase
    {
        private readonly CourseHubDbContext _context;

        public UsersController(CourseHubDbContext context)
        {
            _context = context;
        }

        // GET: api/Users
        [HttpGet]
        public async Task<IActionResult> GetAll()
        {
            var users = await _context.Users.ToListAsync();

            return Ok(users);
        }

        // GET: api/Users/1
        [HttpGet("{id}")]
        public async Task<IActionResult> GetById(int id)
        {
            var user = await _context.Users.FindAsync(id);

            if (user == null)
                return NotFound();

            return Ok(user);
        }
    }
}
```

---

# Question 5

## Create GetAll() and GetById() methods for Courses.

### CoursesController.cs

```csharp
using lab6.Data;
using Microsoft.AspNetCore.Mvc;
using Microsoft.EntityFrameworkCore;

namespace lab6.Controllers
{
    [ApiController]
    [Route("api/[controller]")]
    public class CoursesController : ControllerBase
    {
        private readonly CourseHubDbContext _context;

        public CoursesController(CourseHubDbContext context)
        {
            _context = context;
        }

        // GET: api/Courses
        [HttpGet]
        public async Task<IActionResult> GetAll()
        {
            var courses = await _context.Courses.ToListAsync();

            return Ok(courses);
        }

        // GET: api/Courses/1
        [HttpGet("{id}")]
        public async Task<IActionResult> GetById(int id)
        {
            var course = await _context.Courses.FindAsync(id);

            if (course == null)
                return NotFound();

            return Ok(course);
        }
    }
}
```

---

# Question 6

## Create GetAll() and GetById() methods for Assignments.

### AssignmentsController.cs

```csharp
using lab6.Data;
using Microsoft.AspNetCore.Mvc;
using Microsoft.EntityFrameworkCore;

namespace lab6.Controllers
{
    [ApiController]
    [Route("api/[controller]")]
    public class AssignmentsController : ControllerBase
    {
        private readonly CourseHubDbContext _context;

        public AssignmentsController(CourseHubDbContext context)
        {
            _context = context;
        }

        // GET: api/Assignments
        [HttpGet]
        public async Task<IActionResult> GetAll()
        {
            var assignments = await _context.Assignments.ToListAsync();

            return Ok(assignments);
        }

        // GET: api/Assignments/1
        [HttpGet("{id}")]
        public async Task<IActionResult> GetById(int id)
        {
            var assignment =
                await _context.Assignments.FindAsync(id);

            if (assignment == null)
                return NotFound();

            return Ok(assignment);
        }
    }
}
```

---

# Question 7

## Create GetAll() and GetById() methods for Submissions.

### SubmissionsController.cs

```csharp
using lab6.Data;
using Microsoft.AspNetCore.Mvc;
using Microsoft.EntityFrameworkCore;

namespace lab6.Controllers
{
    [ApiController]
    [Route("api/[controller]")]
    public class SubmissionsController : ControllerBase
    {
        private readonly CourseHubDbContext _context;

        public SubmissionsController(CourseHubDbContext context)
        {
            _context = context;
        }

        // GET: api/Submissions
        [HttpGet]
        public async Task<IActionResult> GetAll()
        {
            var submissions =
                await _context.Submissions.ToListAsync();

            return Ok(submissions);
        }

        // GET: api/Submissions/1
        [HttpGet("{id}")]
        public async Task<IActionResult> GetById(int id)
        {
            var submission =
                await _context.Submissions.FindAsync(id);

            if (submission == null)
                return NotFound();

            return Ok(submission);
        }
    }
}
```

---

# Question 8

## Create Post(), UpdateById() and DeleteById() methods for Roles.

> For POST, we use `StatusCode(201, role)` instead of `CreatedAtAction()` because it is simpler.

```csharp
// POST: api/Roles
[HttpPost]
public async Task<IActionResult> Post(Role role)
{
    _context.Roles.Add(role);

    await _context.SaveChangesAsync();

    return StatusCode(201, role);
}

// PUT: api/Roles/1
[HttpPut("{id}")]
public async Task<IActionResult> UpdateById(
    int id,
    Role role)
{
    var existingRole =
        await _context.Roles.FindAsync(id);

    if (existingRole == null)
        return NotFound();

    existingRole.RoleName = role.RoleName;

    await _context.SaveChangesAsync();

    return Ok(existingRole);
}

// DELETE: api/Roles/1
[HttpDelete("{id}")]
public async Task<IActionResult> DeleteById(int id)
{
    var role = await _context.Roles.FindAsync(id);

    if (role == null)
        return NotFound();

    _context.Roles.Remove(role);

    await _context.SaveChangesAsync();

    return NoContent();
}
```

### POST explanation

```csharp
_context.Roles.Add(role);
```

Adds the role.

```csharp
await _context.SaveChangesAsync();
```

Saves the role in the database.

```csharp
return StatusCode(201, role);
```

Returns:

```text
201 Created
```

---

# Question 9

## Create Post(), UpdateById() and DeleteById() methods for Users.

```csharp
// POST: api/Users
[HttpPost]
public async Task<IActionResult> Post(User user)
{
    _context.Users.Add(user);

    await _context.SaveChangesAsync();

    return StatusCode(201, user);
}

// PUT: api/Users/1
[HttpPut("{id}")]
public async Task<IActionResult> UpdateById(
    int id,
    User user)
{
    var existingUser =
        await _context.Users.FindAsync(id);

    if (existingUser == null)
        return NotFound();

    existingUser.FullName = user.FullName;
    existingUser.Email = user.Email;
    existingUser.RoleId = user.RoleId;
    existingUser.PasswordHash = user.PasswordHash;
    existingUser.IsActive = user.IsActive;

    await _context.SaveChangesAsync();

    return Ok(existingUser);
}

// DELETE: api/Users/1
[HttpDelete("{id}")]
public async Task<IActionResult> DeleteById(int id)
{
    var user = await _context.Users.FindAsync(id);

    if (user == null)
        return NotFound();

    _context.Users.Remove(user);

    await _context.SaveChangesAsync();

    return NoContent();
}
```

---

# Question 10

## Create Post(), UpdateById() and DeleteById() methods for Courses.

```csharp
// POST: api/Courses
[HttpPost]
public async Task<IActionResult> Post(Course course)
{
    _context.Courses.Add(course);

    await _context.SaveChangesAsync();

    return StatusCode(201, course);
}

// PUT: api/Courses/1
[HttpPut("{id}")]
public async Task<IActionResult> UpdateById(
    int id,
    Course course)
{
    var existingCourse =
        await _context.Courses.FindAsync(id);

    if (existingCourse == null)
        return NotFound();

    existingCourse.CourseCode = course.CourseCode;
    existingCourse.CourseTitle = course.CourseTitle;
    existingCourse.FacultyId = course.FacultyId;
    existingCourse.Credits = course.Credits;

    await _context.SaveChangesAsync();

    return Ok(existingCourse);
}

// DELETE: api/Courses/1
[HttpDelete("{id}")]
public async Task<IActionResult> DeleteById(int id)
{
    var course =
        await _context.Courses.FindAsync(id);

    if (course == null)
        return NotFound();

    _context.Courses.Remove(course);

    await _context.SaveChangesAsync();

    return NoContent();
}
```

---

# Question 11

## Create Post(), UpdateById() and DeleteById() methods for Assignments.

```csharp
// POST: api/Assignments
[HttpPost]
public async Task<IActionResult> Post(
    Assignment assignment)
{
    _context.Assignments.Add(assignment);

    await _context.SaveChangesAsync();

    return StatusCode(201, assignment);
}

// PUT: api/Assignments/1
[HttpPut("{id}")]
public async Task<IActionResult> UpdateById(
    int id,
    Assignment assignment)
{
    var existingAssignment =
        await _context.Assignments.FindAsync(id);

    if (existingAssignment == null)
        return NotFound();

    existingAssignment.CourseId = assignment.CourseId;
    existingAssignment.Title = assignment.Title;
    existingAssignment.Description = assignment.Description;
    existingAssignment.DueDate = assignment.DueDate;
    existingAssignment.MaxMarks = assignment.MaxMarks;

    await _context.SaveChangesAsync();

    return Ok(existingAssignment);
}

// DELETE: api/Assignments/1
[HttpDelete("{id}")]
public async Task<IActionResult> DeleteById(int id)
{
    var assignment =
        await _context.Assignments.FindAsync(id);

    if (assignment == null)
        return NotFound();

    _context.Assignments.Remove(assignment);

    await _context.SaveChangesAsync();

    return NoContent();
}
```

---

# Question 12

## Create Post(), UpdateById() and DeleteById() methods for Submissions.

```csharp
// POST: api/Submissions
[HttpPost]
public async Task<IActionResult> Post(
    Submission submission)
{
    _context.Submissions.Add(submission);

    await _context.SaveChangesAsync();

    return StatusCode(201, submission);
}

// PUT: api/Submissions/1
[HttpPut("{id}")]
public async Task<IActionResult> UpdateById(
    int id,
    Submission submission)
{
    var existingSubmission =
        await _context.Submissions.FindAsync(id);

    if (existingSubmission == null)
        return NotFound();

    existingSubmission.AssignmentId =
        submission.AssignmentId;

    existingSubmission.StudentId =
        submission.StudentId;

    existingSubmission.FilePath =
        submission.FilePath;

    existingSubmission.MarksObtained =
        submission.MarksObtained;

    existingSubmission.Remarks =
        submission.Remarks;

    existingSubmission.Status =
        submission.Status;

    await _context.SaveChangesAsync();

    return Ok(existingSubmission);
}

// DELETE: api/Submissions/1
[HttpDelete("{id}")]
public async Task<IActionResult> DeleteById(int id)
{
    var submission =
        await _context.Submissions.FindAsync(id);

    if (submission == null)
        return NotFound();

    _context.Submissions.Remove(submission);

    await _context.SaveChangesAsync();

    return NoContent();
}
```

---

# 13. Simple CRUD Pattern

## Get All

```csharp
[HttpGet]
public async Task<IActionResult> GetAll()
{
    var data = await _context.Table.ToListAsync();

    return Ok(data);
}
```

**Status Code: 200 OK**

---

## Get By ID

```csharp
[HttpGet("{id}")]
public async Task<IActionResult> GetById(int id)
{
    var data = await _context.Table.FindAsync(id);

    if (data == null)
        return NotFound();

    return Ok(data);
}
```

**Status Codes:**

- `200 OK` → Record found
- `404 Not Found` → Record not found

---

## Post

```csharp
[HttpPost]
public async Task<IActionResult> Post(Entity entity)
{
    _context.Table.Add(entity);

    await _context.SaveChangesAsync();

    return StatusCode(201, entity);
}
```

**Status Code: 201 Created**

---

## Update

```csharp
[HttpPut("{id}")]
public async Task<IActionResult> UpdateById(
    int id,
    Entity entity)
{
    var existing =
        await _context.Table.FindAsync(id);

    if (existing == null)
        return NotFound();

    // Update properties here

    await _context.SaveChangesAsync();

    return Ok(existing);
}
```

**Status Codes:**

- `200 OK` → Updated successfully
- `404 Not Found` → Record not found

---

## Delete

```csharp
[HttpDelete("{id}")]
public async Task<IActionResult> DeleteById(int id)
{
    var entity =
        await _context.Table.FindAsync(id);

    if (entity == null)
        return NotFound();

    _context.Table.Remove(entity);

    await _context.SaveChangesAsync();

    return NoContent();
}
```

**Status Codes:**

- `204 No Content` → Deleted successfully
- `404 Not Found` → Record not found

---

# 14. API Endpoint Summary

## Roles

| Method | URL | Purpose |
|---|---|---|
| GET | `/api/Roles` | Get all roles |
| GET | `/api/Roles/1` | Get role by ID |
| POST | `/api/Roles` | Create role |
| PUT | `/api/Roles/1` | Update role |
| DELETE | `/api/Roles/1` | Delete role |

## Users

| Method | URL | Purpose |
|---|---|---|
| GET | `/api/Users` | Get all users |
| GET | `/api/Users/1` | Get user by ID |
| POST | `/api/Users` | Create user |
| PUT | `/api/Users/1` | Update user |
| DELETE | `/api/Users/1` | Delete user |

## Courses

| Method | URL | Purpose |
|---|---|---|
| GET | `/api/Courses` | Get all courses |
| GET | `/api/Courses/1` | Get course by ID |
| POST | `/api/Courses` | Create course |
| PUT | `/api/Courses/1` | Update course |
| DELETE | `/api/Courses/1` | Delete course |

## Assignments

| Method | URL | Purpose |
|---|---|---|
| GET | `/api/Assignments` | Get all assignments |
| GET | `/api/Assignments/1` | Get assignment by ID |
| POST | `/api/Assignments` | Create assignment |
| PUT | `/api/Assignments/1` | Update assignment |
| DELETE | `/api/Assignments/1` | Delete assignment |

## Submissions

| Method | URL | Purpose |
|---|---|---|
| GET | `/api/Submissions` | Get all submissions |
| GET | `/api/Submissions/1` | Get submission by ID |
| POST | `/api/Submissions` | Create submission |
| PUT | `/api/Submissions/1` | Update submission |
| DELETE | `/api/Submissions/1` | Delete submission |

---

# 15. Important Keywords for Viva

## What is Controller?

A controller handles HTTP requests and returns HTTP responses.

## What is DbContext?

`DbContext` is the main EF Core class used to communicate with the database.

## What is DbSet?

`DbSet` represents a database table.

Example:

```csharp
DbSet<Role> Roles
```

represents the Roles table.

## What is Dependency Injection?

Dependency Injection provides required dependencies to a class instead of creating them manually.

## Why use constructor injection?

It allows ASP.NET Core to provide `CourseHubDbContext` automatically.

## What does ToListAsync() do?

It retrieves all records from a table asynchronously.

## What does FindAsync() do?

It finds a record using its primary key.

## What does Add() do?

It adds a new entity to the EF Core change tracker.

## What does Remove() do?

It marks an entity for deletion.

## Why use SaveChangesAsync()?

It saves pending changes to the database.

## What does Ok() return?

`200 OK`.

## What does NotFound() return?

`404 Not Found`.

## What does StatusCode(201, data) return?

`201 Created`.

## What does NoContent() return?

`204 No Content`.

## Difference between GET and POST?

**GET** retrieves data.

**POST** creates new data.

## Difference between PUT and DELETE?

**PUT** updates existing data.

**DELETE** removes existing data.

---

# 16. Final CRUD Flow

```text
                CLIENT
                   |
                   v
             HTTP REQUEST
                   |
                   v
              CONTROLLER
                   |
                   v
          CourseHubDbContext
                   |
                   v
             Entity Framework
                   |
                   v
               DATABASE
                   |
                   v
             HTTP RESPONSE
                   |
                   v
                CLIENT
```

---

# 17. One-Line Exam Revision

```text
GET ALL     → ToListAsync() → 200
GET BY ID   → FindAsync(id) → 200 / 404
POST        → Add() + SaveChangesAsync() → 201
UPDATE      → FindAsync() + change properties + SaveChangesAsync() → 200 / 404
DELETE      → FindAsync() + Remove() + SaveChangesAsync() → 204 / 404
```

---

# 18. Most Important Code to Remember

```csharp
private readonly CourseHubDbContext _context;

public RolesController(CourseHubDbContext context)
{
    _context = context;
}

[HttpGet]
public async Task<IActionResult> GetAll()
{
    var roles = await _context.Roles.ToListAsync();

    return Ok(roles);
}

[HttpGet("{id}")]
public async Task<IActionResult> GetById(int id)
{
    var role = await _context.Roles.FindAsync(id);

    if (role == null)
        return NotFound();

    return Ok(role);
}

[HttpPost]
public async Task<IActionResult> Post(Role role)
{
    _context.Roles.Add(role);

    await _context.SaveChangesAsync();

    return StatusCode(201, role);
}

[HttpPut("{id}")]
public async Task<IActionResult> UpdateById(
    int id,
    Role role)
{
    var existingRole =
        await _context.Roles.FindAsync(id);

    if (existingRole == null)
        return NotFound();

    existingRole.RoleName = role.RoleName;

    await _context.SaveChangesAsync();

    return Ok(existingRole);
}

[HttpDelete("{id}")]
public async Task<IActionResult> DeleteById(int id)
{
    var role = await _context.Roles.FindAsync(id);

    if (role == null)
        return NotFound();

    _context.Roles.Remove(role);

    await _context.SaveChangesAsync();

    return NoContent();
}
```

---

# 19. Final Status Code Table

| Operation | Success Status | Failure Status |
|---|---:|---:|
| GetAll | 200 | — |
| GetById | 200 | 404 |
| Post | 201 | — |
| UpdateById | 200 | 404 |
| DeleteById | 204 | 404 |

---

# 20. Lab Checklist

- [ ] Create `RolesController`
- [ ] Create `UsersController`
- [ ] Create `CoursesController`
- [ ] Create `AssignmentsController`
- [ ] Create `SubmissionsController`
- [ ] Inject `CourseHubDbContext`
- [ ] Implement `GetAll()`
- [ ] Implement `GetById()`
- [ ] Implement `Post()`
- [ ] Implement `UpdateById()`
- [ ] Implement `DeleteById()`
- [ ] Test APIs using Scalar
- [ ] Verify status codes
