# Lab 4 - ASP.NET Core Students API

## A. Overview

This lab demonstrates how to create a simple ASP.NET Core Web API using:

- **Student model**: Stores student details and subjects.
- **Subject model**: Stores subject details.
- **Static collection**: Stores five student records in memory.
- **Students controller**: Provides endpoints to retrieve student data.

---

## B. Subject Model

Create `Models/Subject.cs`:

```csharp
namespace lab4A.Models
{
    public class Subject
    {
        public int Id { get; set; }

        public string Name { get; set; } = string.Empty;
    }
}
```

### Explanation

- `Id` uniquely identifies a subject.
- `Name` contains the subject name.
- `string.Empty` prevents the name property from being null.

---

## C. Student Model

Create `Models/Student.cs`:

```csharp
namespace lab4A.Models
{
    public class Student
    {
        public int Id { get; set; }

        public string Name { get; set; } = string.Empty;

        public List<Subject> Subjects { get; set; } = new();
    }
}
```

### Explanation

- `Id` uniquely identifies a student.
- `Name` contains the student's name.
- `Subjects` stores a list of `Subject` objects.
- `new()` creates an empty subject list by default.

---

## D. Students Controller

Create `Controllers/StudentsController.cs`:

```csharp
using lab4A.Models;
using Microsoft.AspNetCore.Mvc;

namespace lab4A.Controllers
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
                Subjects = new List<Subject>
                {
                    new Subject { Id = 101, Name = "C#" },
                    new Subject { Id = 102, Name = "ASP.NET Core" },
                    new Subject { Id = 103, Name = "Database" }
                }
            },
            new Student
            {
                Id = 2,
                Name = "Diya Shah",
                Subjects = new List<Subject>
                {
                    new Subject { Id = 101, Name = "C#" },
                    new Subject { Id = 104, Name = "Web Development" }
                }
            },
            new Student
            {
                Id = 3,
                Name = "Kabir Mehta",
                Subjects = new List<Subject>
                {
                    new Subject { Id = 102, Name = "ASP.NET Core" },
                    new Subject { Id = 105, Name = "Cloud Computing" }
                }
            },
            new Student
            {
                Id = 4,
                Name = "Isha Desai",
                Subjects = new List<Subject>
                {
                    new Subject { Id = 103, Name = "Database" },
                    new Subject { Id = 106, Name = "Data Structures" }
                }
            },
            new Student
            {
                Id = 5,
                Name = "Vivaan Joshi",
                Subjects = new List<Subject>
                {
                    new Subject { Id = 101, Name = "C#" },
                    new Subject { Id = 102, Name = "ASP.NET Core" },
                    new Subject { Id = 106, Name = "Data Structures" }
                }
            }
        };

        // GET: api/students
        [HttpGet]
        public ActionResult<List<Student>> GetAllStudents()
        {
            return Ok(Students);
        }

        // GET: api/students/1
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

        // GET: api/students/1/subjects
        [HttpGet("{id:int}/subjects")]
        public ActionResult<List<Subject>> GetStudentSubjects(int id)
        {
            Student? student = Students
                .FirstOrDefault(student => student.Id == id);

            if (student == null)
            {
                return NotFound($"Student with ID {id} was not found.");
            }

            return Ok(student.Subjects);
        }
    }
}
```

### Explanation

- `static readonly` keeps the student list available in memory.
- `[Route("api/[controller]")]` maps this controller to `api/Students`.
- `[HttpGet]` handles HTTP GET requests.
- `Ok()` returns status `200 OK`.
- `NotFound()` returns status `404 Not Found`.
- `FirstOrDefault()` finds the first student matching an ID.

---

## E. API Endpoints

| Purpose | HTTP Method | URL |
|---|---|---|
| Fetch all students | `GET` | `/api/students` |
| Fetch one student | `GET` | `/api/students/1` |
| Fetch one student's subjects | `GET` | `/api/students/1/subjects` |

A student that does not exist should return:

```text
404 Not Found
Student with ID 99 was not found.
```
