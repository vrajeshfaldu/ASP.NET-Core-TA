# Lab 5 — Student API and HTTP Status Codes

## A. HTTP Status Codes

An HTTP status code tells the client what happened when the API handled a request.

| Code | Name | Meaning |
|---:|---|---|
| `200` | OK | The request succeeded and data is returned. |
| `201` | Created | A new student was created. |
| `204` | No Content | The operation succeeded, with no response body. |
| `400` | Bad Request | The request contains invalid data. |
| `404` | Not Found | The requested student does not exist. |
| `409` | Conflict | A student with that ID already exists. |

In ASP.NET Core, common responses include `Ok(...)`, `NotFound()`, `BadRequest(...)`, `Conflict(...)`, and `NoContent()`. You can also return a specific status code with `StatusCode(...)`.

---

## B. Question 1 — Create the Student Model

Create `Models/Student.cs`:

```csharp
namespace lab5.Models
{
    public class Student
    {
        public int Id { get; set; }

        public string Name { get; set; } = string.Empty;

        public int Age { get; set; }
    }
}
```

### Explanation

- `Id` identifies each student.
- `Name` stores the student's name.
- `Age` stores the student's age.
- `string.Empty` provides a default value for `Name`.

---

## C. Question 2 — Create the Students Controller

Create `Controllers/StudentController.cs`:

```csharp
using System.Collections.Generic;
using System.Linq;
using lab5.Models;
using Microsoft.AspNetCore.Mvc;

namespace lab5.Controllers
{
    [Route("api/[controller]")]
    [ApiController]
    public class StudentController : ControllerBase
    {
        private static List<Student> students = new List<Student>();

        [HttpGet]
        public IActionResult GetAllStudents()
        {
            return StatusCode(200, students);
        }

        [HttpGet("{id}")]
        public IActionResult GetById(int id)
        {
            var student = students.FirstOrDefault(s => s.Id == id);

            if (student == null)
            {
                return NotFound();
            }

            return StatusCode(200, student);
        }

        [HttpPost]
        public IActionResult CreateStudent([FromBody] Student student)
        {
            if (student == null)
            {
                return BadRequest("Invalid student data.");
            }

            if (students.Any(s => s.Id == student.Id))
            {
                return Conflict("Student already exists.");
            }

            students.Add(student);

            return StatusCode(201, student);
        }

        [HttpPut("{id}")]
        public IActionResult UpdateStudent(int id, [FromBody] Student updatedStudent)
        {
            if (updatedStudent == null)
            {
                return BadRequest("Invalid student data.");
            }

            var student = students.FirstOrDefault(s => s.Id == id);

            if (student == null)
            {
                return NotFound();
            }

            student.Name = updatedStudent.Name;
            student.Age = updatedStudent.Age;

            return StatusCode(200, student);
        }

        [HttpDelete("{id}")]
        public IActionResult DeleteStudent(int id)
        {
            var student = students.FirstOrDefault(s => s.Id == id);

            if (student == null)
            {
                return NotFound();
            }

            students.Remove(student);

            return NoContent();
        }
    }
}
```

### Explanation

- `[Route("api/[controller]")]` sets the base route to `/api/student`.
- `[HttpGet]` retrieves all students.
- `[HttpGet("{id}")]` retrieves one student by ID.
- `[HttpPost]` adds a student. The `[FromBody]` attribute reads the student from the request body.
- `[HttpPut("{id}")]` updates the student with the matching ID.
- `[HttpDelete("{id}")]` removes the student with the matching ID.
- `FirstOrDefault()` searches for a student and returns `null` if no match is found.
- The `students` list is static and stored in memory. It starts empty and resets when the application restarts.

---

## D. Endpoint Summary

| Operation | HTTP Method | URL | Response |
|---|---|---|---|
| Get all students | `GET` | `/api/student` | `200 OK` |
| Get a student by ID | `GET` | `/api/student/1` | `200 OK` or `404 Not Found` |
| Create a student | `POST` | `/api/student` | `201 Created`, `400 Bad Request`, or `409 Conflict` |
| Update a student | `PUT` | `/api/student/1` | `200 OK`, `400 Bad Request`, or `404 Not Found` |
| Delete a student | `DELETE` | `/api/student/1` | `204 No Content` or `404 Not Found` |

---

## E. Example POST Request

Send a `POST` request to `/api/student` with this JSON body:

```json
{
  "id": 1,
  "name": "Aarav Patel",
  "age": 20
}
```

A successful request returns status `201 Created`.

## F. Example PUT Request

Send a `PUT` request to `/api/student/1` with this JSON body:

```json
{
  "id": 1,
  "name": "Aarav Patel",
  "age": 21
}
```

The ID in the URL selects the student to update.
