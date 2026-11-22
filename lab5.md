# Lab 5 — ASP.NET Core Status Codes

## A. HTTP Status Code Explanation

HTTP status codes describe the result of an API request.

### 2xx — Success

| Code | Name | Meaning |
|---:|---|---|
| `200` | OK | The request was successful and data is returned. |
| `201` | Created | A new resource was successfully created. |
| `204` | No Content | The operation was successful, but no response body is returned. |

### 4xx — Client Error

| Code | Name | Meaning |
|---:|---|---|
| `400` | Bad Request | The request data or parameters are invalid. |
| `404` | Not Found | The requested resource does not exist. |
| `409` | Conflict | The request conflicts with the current state. |

### 5xx — Server Error

| Code | Name | Meaning |
|---:|---|---|
| `500` | Internal Server Error | An unexpected server-side error occurred. |

ASP.NET Core provides methods for commonly used status codes:

```csharp
Ok(value);       // 200 OK
Created(...);    // 201 Created
NoContent();     // 204 No Content
BadRequest();    // 400 Bad Request
NotFound();      // 404 Not Found
Conflict();      // 409 Conflict
```

---

## B. Question 1 — Create a Student Model

Create `Models/Student.cs`.

```csharp
namespace lab5.Models
{
    public class Student
    {
        public int Id { get; set; }

        public string Name { get; set; } = string.Empty;

        public int Age { get; set; }

        public string Course { get; set; } = string.Empty;
    }
}
```

### Explanation

- `Id` is the unique student identifier.
- `Name` stores the student's name.
- `Age` stores the student's age.
- `Course` stores the course name.

---

## C. Question 2 — Pre-load Student Records

Add a static list inside `StudentsController`.

```csharp
private static readonly List<Student> Students = new()
{
    new Student
    {
        Id = 1,
        Name = "Aarav Patel",
        Age = 20,
        Course = "CSE"
    },
    new Student
    {
        Id = 2,
        Name = "Diya Shah",
        Age = 21,
        Course = "IT"
    },
    new Student
    {
        Id = 3,
        Name = "Kabir Mehta",
        Age = 20,
        Course = "CSE"
    },
    new Student
    {
        Id = 4,
        Name = "Isha Desai",
        Age = 22,
        Course = "ECE"
    },
    new Student
    {
        Id = 5,
        Name = "Vivaan Joshi",
        Age = 21,
        Course = "IT"
    }
};
```

### Explanation

- `static` stores the list for the lifetime of the application.
- `readonly` prevents the list variable from being reassigned.
- The list acts as an in-memory database.

---

## D. Question 3 — Get All Students

### Status Code

`GET /api/students` normally returns:

- `200 OK` when students are returned.
- `204 No Content` when the list is empty.

Add this action to `StudentsController`.

```csharp
[HttpGet]
public ActionResult<List<Student>> GetAllStudents()
{
    if (Students.Count == 0)
    {
        return NoContent();
    }

    return Ok(Students);
}
```

### Complete Initial Controller

Create `Controllers/StudentsController.cs`:

```csharp
using lab5.Models;
using Microsoft.AspNetCore.Mvc;

namespace lab5.Controllers
{
    [ApiController]
    [Route("api/[controller]")]
    public class StudentsController : ControllerBase
    {
        private static readonly List<Student> Students = new()
        {
            new Student
            {
                Id = 1,
                Name = "Aarav Patel",
                Age = 20,
                Course = "CSE"
            },
            new Student
            {
                Id = 2,
                Name = "Diya Shah",
                Age = 21,
                Course = "IT"
            },
            new Student
            {
                Id = 3,
                Name = "Kabir Mehta",
                Age = 20,
                Course = "CSE"
            },
            new Student
            {
                Id = 4,
                Name = "Isha Desai",
                Age = 22,
                Course = "ECE"
            },
            new Student
            {
                Id = 5,
                Name = "Vivaan Joshi",
                Age = 21,
                Course = "IT"
            }
        };

        [HttpGet]
        public ActionResult<List<Student>> GetAllStudents()
        {
            if (Students.Count == 0)
            {
                return NoContent();
            }

            return Ok(Students);
        }
    }
}
```

---

## E. Question 4 — Get a Student by ID

### Status Codes

- `200 OK`: Student found.
- `404 Not Found`: Student ID does not exist.

```csharp
[HttpGet("{id:int}")]
public ActionResult<Student> GetStudentById(int id)
{
    Student? student = Students
        .FirstOrDefault(student => student.Id == id);

    if (student == null)
    {
        return NotFound($"Student with ID {id} was not found.");
    }

    return Ok(student);
}
```

### Example Endpoints

```text
GET /api/students/1
```

```text
GET /api/students/99
```

The second request returns `404 Not Found`.

---

## F. Question 5 — Add a New Student

### Status Codes

- `201 Created`: Student was added successfully.
- `400 Bad Request`: Student data is invalid.

```csharp
[HttpPost]
public ActionResult<Student> CreateStudent(Student student)
{
    if (student == null ||
        string.IsNullOrWhiteSpace(student.Name) ||
        student.Age <= 0)
    {
        return BadRequest("Invalid student data.");
    }

    if (Students.Any(existing => existing.Id == student.Id))
    {
        return Conflict($"Student with ID {student.Id} already exists.");
    }

    Students.Add(student);

    return CreatedAtAction(
        nameof(GetStudentById),
        new { id = student.Id },
        student);
}
```

### Explanation

- `201 Created` means a resource was successfully created.
- `BadRequest()` returns `400`.
- `Conflict()` returns `409` when the ID already exists.
- `CreatedAtAction()` includes a link to the new student's endpoint.

### Example JSON

```json
{
  "id": 6,
  "name": "Neel Mehta",
  "age": 20,
  "course": "CSE"
}
```

---

## G. Question 6 — Update a Student

### Status Codes

- `204 No Content`: Student updated successfully.
- `404 Not Found`: Student does not exist.
- `400 Bad Request`: Updated data is invalid.

```csharp
[HttpPut("{id:int}")]
public IActionResult UpdateStudent(int id, Student updatedStudent)
{
    Student? existingStudent = Students
        .FirstOrDefault(student => student.Id == id);

    if (existingStudent == null)
    {
        return NotFound($"Student with ID {id} was not found.");
    }

    if (string.IsNullOrWhiteSpace(updatedStudent.Name) ||
        updatedStudent.Age <= 0)
    {
        return BadRequest("Invalid student data.");
    }

    existingStudent.Name = updatedStudent.Name;
    existingStudent.Age = updatedStudent.Age;
    existingStudent.Course = updatedStudent.Course;

    return NoContent();
}
```

---

## H. Question 7 — Delete a Student

### Status Codes

- `204 No Content`: Student deleted successfully.
- `404 Not Found`: Student does not exist.

```csharp
[HttpDelete("{id:int}")]
public IActionResult DeleteStudent(int id)
{
    Student? student = Students
        .FirstOrDefault(student => student.Id == id);

    if (student == null)
    {
        return NotFound($"Student with ID {id} was not found.");
    }

    Students.Remove(student);

    return NoContent();
}
```

---

## I. Complete Controller

```csharp
using lab5.Models;
using Microsoft.AspNetCore.Mvc;

namespace lab5.Controllers
{
    [ApiController]
    [Route("api/[controller]")]
    public class StudentsController : ControllerBase
    {
        private static readonly List<Student> Students = new()
        {
            new Student
            {
                Id = 1,
                Name = "Aarav Patel",
                Age = 20,
                Course = "CSE"
            },
            new Student
            {
                Id = 2,
                Name = "Diya Shah",
                Age = 21,
                Course = "IT"
            },
            new Student
            {
                Id = 3,
                Name = "Kabir Mehta",
                Age = 20,
                Course = "CSE"
            },
            new Student
            {
                Id = 4,
                Name = "Isha Desai",
                Age = 22,
                Course = "ECE"
            },
            new Student
            {
                Id = 5,
                Name = "Vivaan Joshi",
                Age = 21,
                Course = "IT"
            }
        };

        [HttpGet]
        public ActionResult<List<Student>> GetAllStudents()
        {
            if (Students.Count == 0)
            {
                return NoContent();
            }

            return Ok(Students);
        }

        [HttpGet("{id:int}")]
        public ActionResult<Student> GetStudentById(int id)
        {
            Student? student = Students
                .FirstOrDefault(student => student.Id == id);

            if (student == null)
            {
                return NotFound($"Student with ID {id} was not found.");
            }

            return Ok(student);
        }

        [HttpPost]
        public ActionResult<Student> CreateStudent(Student student)
        {
            if (student == null ||
                string.IsNullOrWhiteSpace(student.Name) ||
                student.Age <= 0)
            {
                return BadRequest("Invalid student data.");
            }

            if (Students.Any(existing => existing.Id == student.Id))
            {
                return Conflict($"Student with ID {student.Id} already exists.");
            }

            Students.Add(student);

            return CreatedAtAction(
                nameof(GetStudentById),
                new { id = student.Id },
                student);
        }

        [HttpPut("{id:int}")]
        public IActionResult UpdateStudent(int id, Student updatedStudent)
        {
            Student? existingStudent = Students
                .FirstOrDefault(student => student.Id == id);

            if (existingStudent == null)
            {
                return NotFound($"Student with ID {id} was not found.");
            }

            if (string.IsNullOrWhiteSpace(updatedStudent.Name) ||
                updatedStudent.Age <= 0)
            {
                return BadRequest("Invalid student data.");
            }

            existingStudent.Name = updatedStudent.Name;
            existingStudent.Age = updatedStudent.Age;
            existingStudent.Course = updatedStudent.Course;

            return NoContent();
        }

        [HttpDelete("{id:int}")]
        public IActionResult DeleteStudent(int id)
        {
            Student? student = Students
                .FirstOrDefault(student => student.Id == id);

            if (student == null)
            {
                return NotFound($"Student with ID {id} was not found.");
            }

            Students.Remove(student);

            return NoContent();
        }
    }
}
```

---

## J. Endpoint Summary

| Operation | Endpoint | Success Status |
|---|---|---:|
| Get all students | `GET /api/students` | `200` or `204` |
| Get student by ID | `GET /api/students/1` | `200` |
| Create student | `POST /api/students` | `201` |
| Update student | `PUT /api/students/1` | `204` |
| Delete student | `DELETE /api/students/1` | `204` |

| Error | Status Code |
|---|---:|
| Invalid input | `400 Bad Request` |
| Resource not found | `404 Not Found` |
| Duplicate ID | `409 Conflict` |
| Unexpected server error | `500 Internal Server Error` |
