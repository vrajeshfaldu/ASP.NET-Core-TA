# Lab 11 – LINQ using Projection, Conditional and OrderBy Operators

## Aim

To use LINQ queries with Entity Framework Core for:

- Projection using `Select()`
- Filtering using `Where()`
- Conditional queries
- Sorting using `OrderBy()` and `OrderByDescending()`
- Multiple sorting using `ThenBy()`
- Getting the first matching record using `FirstOrDefault()`
- Getting distinct values using `Distinct()`
- Limiting results using `Take()`
- Skipping records using `Skip()`
- Combining multiple LINQ operators

---

# Concept Overview

## 1. What is LINQ?

LINQ stands for **Language Integrated Query**.

It allows us to query data using C# syntax.

Example:

```csharp
var courses = await _context.Courses
    .Where(c => c.Credits > 3)
    .ToListAsync();
```

Here:

- `_context.Courses` → Courses table
- `Where()` → filters records
- `ToListAsync()` → executes the query and returns a list

---

## 2. Select()

`Select()` is used for **projection**.

It allows us to select only the columns we need.

Example:

```csharp
var result = await _context.Courses
    .Select(c => new
    {
        c.CourseCode,
        c.CourseTitle
    })
    .ToListAsync();
```

Instead of returning all course columns, it returns only:

- CourseCode
- CourseTitle

---

## 3. Where()

`Where()` is used to filter records based on a condition.

Example:

```csharp
var result = await _context.Courses
    .Where(c => c.Credits > 3)
    .ToListAsync();
```

Only courses having more than 3 credits are returned.

---

## 4. Contains()

`Contains()` checks whether a string contains specific text.

Example:

```csharp
.Where(c => c.CourseTitle.Contains("Programming"))
```

This finds courses whose title contains `"Programming"`.

---

## 5. StartsWith()

`StartsWith()` checks whether a string starts with specific text.

Example:

```csharp
.Where(c => c.CourseCode.StartsWith("2401"))
```

---

## 6. FirstOrDefault()

`FirstOrDefault()` returns the first matching record.

If no record is found, it returns `null`.

Example:

```csharp
var user = await _context.Users
    .FirstOrDefaultAsync(u => u.Email == email);
```

---

## 7. OrderBy()

`OrderBy()` sorts data in ascending order.

Example:

```csharp
.OrderBy(c => c.CourseTitle)
```

For text, this normally means A-Z.

---

## 8. OrderByDescending()

`OrderByDescending()` sorts data in descending order.

Example:

```csharp
.OrderByDescending(a => a.DueDate)
```

Latest dates come first.

---

## 9. ThenBy()

`ThenBy()` provides a second sorting condition.

Example:

```csharp
.OrderByDescending(c => c.Credits)
.ThenBy(c => c.CourseTitle)
```

First sort by credits from highest to lowest.

If two courses have the same credits, sort their titles A-Z.

---

## 10. Distinct()

`Distinct()` removes duplicate values.

Example:

```csharp
var result = await _context.Submissions
    .Select(s => s.Status)
    .Distinct()
    .ToListAsync();
```

---

## 11. Take()

`Take()` returns only the specified number of records.

Example:

```csharp
.Take(5)
```

Returns maximum 5 records.

---

## 12. Skip()

`Skip()` skips the specified number of records.

Example:

```csharp
.Skip(5)
.Take(5)
```

This can be used for pagination.

For page 2 with page size 5:

```text
Skip = (PageNumber - 1) × PageSize
Skip = (2 - 1) × 5
Skip = 5
```

---

# Lab Questions and Solutions

## Question 1

### Retrieve all courses with all columns.

```csharp
[HttpGet("1")]
public async Task<IActionResult> GetAllCourses()
{
    var result = await _context.Courses
        .ToListAsync();

    return Ok(result);
}
```

### Explanation

`ToListAsync()` retrieves all course records with all columns.

---

## Question 2

### Retrieve only CourseCode and CourseTitle of all courses.

```csharp
[HttpGet("2")]
public async Task<IActionResult> GetCourseCodeAndTitle()
{
    var result = await _context.Courses
        .Select(c => new
        {
            c.CourseCode,
            c.CourseTitle
        })
        .ToListAsync();

    return Ok(result);
}
```

### Explanation

`Select()` is used to project only the required columns.

---

## Question 3

### Retrieve full name and email of all users. PasswordHash should not be included.

```csharp
[HttpGet("3")]
public async Task<IActionResult> GetUserNamesAndEmails()
{
    var result = await _context.Users
        .Select(u => new
        {
            u.FullName,
            u.Email
        })
        .ToListAsync();

    return Ok(result);
}
```

### Explanation

Only `FullName` and `Email` are selected, so `PasswordHash` is not returned.

---

## Question 4

### Retrieve titles of all assignments as a simple list of strings.

```csharp
[HttpGet("4")]
public async Task<IActionResult> GetAssignmentTitles()
{
    var result = await _context.Assignments
        .Select(a => a.Title)
        .ToListAsync();

    return Ok(result);
}
```

### Explanation

Because only `Title` is selected, the result is a list of strings.

---

## Question 5

### Retrieve all courses with more than 3 credits.

```csharp
[HttpGet("5")]
public async Task<IActionResult> GetCoursesMoreThan3Credits()
{
    var result = await _context.Courses
        .Where(c => c.Credits > 3)
        .ToListAsync();

    return Ok(result);
}
```

### Explanation

`Where()` filters courses where `Credits > 3`.

---

## Question 6

### Retrieve all courses whose title contains "Programming".

```csharp
[HttpGet("6")]
public async Task<IActionResult> GetProgrammingCourses()
{
    var result = await _context.Courses
        .Where(c => c.CourseTitle.Contains("Programming"))
        .ToListAsync();

    return Ok(result);
}
```

### Explanation

`Contains()` checks whether `"Programming"` exists in the course title.

---

## Question 7

### Retrieve all courses whose code starts with "2401".

```csharp
[HttpGet("7")]
public async Task<IActionResult> GetCoursesStartingWith2401()
{
    var result = await _context.Courses
        .Where(c => c.CourseCode.StartsWith("2401"))
        .ToListAsync();

    return Ok(result);
}
```

### Explanation

`StartsWith("2401")` returns course codes beginning with `2401`.

---

## Question 8

### Retrieve the first user whose email matches a given value. Return null if no match is found.

```csharp
[HttpGet("8")]
public async Task<IActionResult> GetUserByEmail(string email)
{
    var result = await _context.Users
        .FirstOrDefaultAsync(u => u.Email == email);

    return Ok(result);
}
```

### Explanation

`FirstOrDefaultAsync()` returns the first matching user.

If no user is found, the result is `null`.

---

## Question 9

### Retrieve all courses ordered alphabetically by CourseTitle.

```csharp
[HttpGet("9")]
public async Task<IActionResult> GetCoursesOrderedByTitle()
{
    var result = await _context.Courses
        .OrderBy(c => c.CourseTitle)
        .ToListAsync();

    return Ok(result);
}
```

### Explanation

`OrderBy()` sorts the course titles in ascending order.

---

## Question 10

### Retrieve all assignments ordered by DueDate, latest due date first.

```csharp
[HttpGet("10")]
public async Task<IActionResult> GetAssignmentsByLatestDueDate()
{
    var result = await _context.Assignments
        .OrderByDescending(a => a.DueDate)
        .ToListAsync();

    return Ok(result);
}
```

### Explanation

`OrderByDescending()` puts the latest due date first.

---

## Question 11

### Retrieve all active users who have Faculty role.

```csharp
[HttpGet("11")]
public async Task<IActionResult> GetActiveFacultyUsers()
{
    var result = await (
        from u in _context.Users
        join r in _context.Roles
            on u.RoleId equals r.RoleId
        where u.IsActive == true
              && u.IsDeleted == false
              && r.RoleName == "Faculty"
        select u
    ).ToListAsync();

    return Ok(result);
}
```

### Explanation

The query:

1. Joins `Users` and `Roles`.
2. Matches `RoleId`.
3. Checks that the user is active.
4. Checks that the user is not deleted.
5. Checks that the role is `Faculty`.

---

## Question 12

### Retrieve all assignments due in the next 7 days.

```csharp
[HttpGet("12")]
public async Task<IActionResult> GetAssignmentsNext7Days()
{
    var today = DateTime.UtcNow;
    var next7Days = today.AddDays(7);

    var result = await _context.Assignments
        .Where(a => a.DueDate >= today &&
                    a.DueDate <= next7Days)
        .ToListAsync();

    return Ok(result);
}
```

### Explanation

`today` represents the current UTC date/time.

`next7Days` represents the date/time 7 days from now.

The query returns assignments between these two values.

---

## Question 13

### Retrieve all courses ordered by Credits highest first, then CourseTitle A-Z for equal credits.

```csharp
[HttpGet("13")]
public async Task<IActionResult> GetCoursesByCreditsAndTitle()
{
    var result = await _context.Courses
        .OrderByDescending(c => c.Credits)
        .ThenBy(c => c.CourseTitle)
        .ToListAsync();

    return Ok(result);
}
```

### Explanation

First:

```csharp
.OrderByDescending(c => c.Credits)
```

sorts credits from highest to lowest.

Then:

```csharp
.ThenBy(c => c.CourseTitle)
```

sorts equal-credit courses by title A-Z.

---

## Question 14

### Retrieve each course as "CourseCode - CourseTitle" in a single column called DisplayName.

```csharp
[HttpGet("14")]
public async Task<IActionResult> GetCourseDisplayNames()
{
    var result = await _context.Courses
        .Select(c => new
        {
            DisplayName = c.CourseCode + " - " + c.CourseTitle
        })
        .ToListAsync();

    return Ok(result);
}
```

### Example output

```text
2401CS101 - C# Programming
2401CS102 - ASP.NET Core
```

---

## Question 15

### Retrieve title and MaxMarks plus calculated PassMarks = 40% of MaxMarks.

```csharp
[HttpGet("15")]
public async Task<IActionResult> GetAssignmentMarks()
{
    var result = await _context.Assignments
        .Select(a => new
        {
            a.Title,
            a.MaxMarks,
            PassMarks = a.MaxMarks * 0.40
        })
        .ToListAsync();

    return Ok(result);
}
```

### Explanation

`PassMarks` is calculated during projection.

For example:

```text
MaxMarks = 100
PassMarks = 100 × 0.40
PassMarks = 40
```

---

## Question 16

### Retrieve distinct submission statuses, alphabetically.

```csharp
[HttpGet("16")]
public async Task<IActionResult> GetSubmissionStatuses()
{
    var result = await _context.Submissions
        .Select(s => s.Status)
        .Distinct()
        .OrderBy(s => s)
        .ToListAsync();

    return Ok(result);
}
```

### Explanation

`Distinct()` removes duplicate statuses.

`OrderBy()` sorts the remaining statuses alphabetically.

---

## Question 17

### Retrieve submissions that are either Late or have no marks, projecting SubmissionId, Status and MarksObtained.

```csharp
[HttpGet("17")]
public async Task<IActionResult> GetLateOrUngradedSubmissions()
{
    var result = await _context.Submissions
        .Where(s => s.Status == "Late" ||
                    s.MarksObtained == null)
        .Select(s => new
        {
            s.SubmissionId,
            s.Status,
            s.MarksObtained
        })
        .ToListAsync();

    return Ok(result);
}
```

### Explanation

The `||` operator means OR.

So a submission is returned when:

- Status is `Late`

OR

- MarksObtained is `null`

---

## Question 18

### Retrieve top 5 submissions with highest MarksObtained, ignoring ungraded submissions.

```csharp
[HttpGet("18")]
public async Task<IActionResult> GetTop5Submissions()
{
    var result = await _context.Submissions
        .Where(s => s.MarksObtained != null)
        .OrderByDescending(s => s.MarksObtained)
        .Take(5)
        .Select(s => new
        {
            s.SubmissionId,
            s.StudentId,
            s.MarksObtained
        })
        .ToListAsync();

    return Ok(result);
}
```

### Explanation

The order of operations is:

```text
Where → OrderByDescending → Take → Select
```

1. Ignore submissions without marks.
2. Sort by marks highest first.
3. Take top 5.
4. Return only required columns.

---

## Question 19

### Retrieve 3 most recently created, non-deleted courses. Show only CourseCode, CourseTitle and CreatedAt.

```csharp
[HttpGet("19")]
public async Task<IActionResult> GetRecentCourses()
{
    var result = await _context.Courses
        .Where(c => c.IsDeleted == false)
        .OrderByDescending(c => c.CreatedAt)
        .Take(3)
        .Select(c => new
        {
            c.CourseCode,
            c.CourseTitle,
            c.CreatedAt
        })
        .ToListAsync();

    return Ok(result);
}
```

### Explanation

The query:

1. Removes deleted courses.
2. Sorts newest courses first.
3. Takes 3 courses.
4. Projects only the required columns.

---

## Question 20

### Retrieve active students ordered by FullName, skip first 5 and return next 5.

```csharp
[HttpGet("20")]
public async Task<IActionResult> GetActiveStudentsPage2()
{
    var result = await (
        from u in _context.Users
        join r in _context.Roles
            on u.RoleId equals r.RoleId
        where u.IsActive == true
              && u.IsDeleted == false
              && r.RoleName == "Student"
        orderby u.FullName
        select u
    )
    .Skip(5)
    .Take(5)
    .ToListAsync();

    return Ok(result);
}
```

### Explanation

First, students are:

- Active
- Not deleted
- Having Student role

Then they are ordered by `FullName`.

```csharp
.Skip(5)
.Take(5)
```

means:

- Skip first 5 students.
- Return the next 5 students.

This represents page 2 when page size is 5.

---

# Complete LinqController

Create a separate controller named:

```text
Controllers
    └── LinqController.cs
```

Use the following complete code:

```csharp
using Microsoft.AspNetCore.Mvc;
using Microsoft.EntityFrameworkCore;

namespace lab6.Controllers
{
    [Route("api/[controller]/[action]")]
    [ApiController]
    public class LinqController : ControllerBase
    {
        private readonly CourseHubDbContext _context;

        public LinqController(CourseHubDbContext context)
        {
            _context = context;
        }

        [HttpGet("1")]
        public async Task<IActionResult> GetAllCourses()
        {
            var result = await _context.Courses.ToListAsync();
            return Ok(result);
        }

        [HttpGet("2")]
        public async Task<IActionResult> GetCourseCodeAndTitle()
        {
            var result = await _context.Courses
                .Select(c => new { c.CourseCode, c.CourseTitle })
                .ToListAsync();

            return Ok(result);
        }

        [HttpGet("3")]
        public async Task<IActionResult> GetUserNamesAndEmails()
        {
            var result = await _context.Users
                .Select(u => new { u.FullName, u.Email })
                .ToListAsync();

            return Ok(result);
        }

        [HttpGet("4")]
        public async Task<IActionResult> GetAssignmentTitles()
        {
            var result = await _context.Assignments
                .Select(a => a.Title)
                .ToListAsync();

            return Ok(result);
        }

        [HttpGet("5")]
        public async Task<IActionResult> GetCoursesMoreThan3Credits()
        {
            var result = await _context.Courses
                .Where(c => c.Credits > 3)
                .ToListAsync();

            return Ok(result);
        }

        [HttpGet("6")]
        public async Task<IActionResult> GetProgrammingCourses()
        {
            var result = await _context.Courses
                .Where(c => c.CourseTitle.Contains("Programming"))
                .ToListAsync();

            return Ok(result);
        }

        [HttpGet("7")]
        public async Task<IActionResult> GetCoursesStartingWith2401()
        {
            var result = await _context.Courses
                .Where(c => c.CourseCode.StartsWith("2401"))
                .ToListAsync();

            return Ok(result);
        }

        [HttpGet("8")]
        public async Task<IActionResult> GetUserByEmail(string email)
        {
            var result = await _context.Users
                .FirstOrDefaultAsync(u => u.Email == email);

            return Ok(result);
        }

        [HttpGet("9")]
        public async Task<IActionResult> GetCoursesOrderedByTitle()
        {
            var result = await _context.Courses
                .OrderBy(c => c.CourseTitle)
                .ToListAsync();

            return Ok(result);
        }

        [HttpGet("10")]
        public async Task<IActionResult> GetAssignmentsByLatestDueDate()
        {
            var result = await _context.Assignments
                .OrderByDescending(a => a.DueDate)
                .ToListAsync();

            return Ok(result);
        }

        [HttpGet("11")]
        public async Task<IActionResult> GetActiveFacultyUsers()
        {
            var result = await (
                from u in _context.Users
                join r in _context.Roles
                    on u.RoleId equals r.RoleId
                where u.IsActive == true
                      && u.IsDeleted == false
                      && r.RoleName == "Faculty"
                select u
            ).ToListAsync();

            return Ok(result);
        }

        [HttpGet("12")]
        public async Task<IActionResult> GetAssignmentsNext7Days()
        {
            var today = DateTime.UtcNow;
            var next7Days = today.AddDays(7);

            var result = await _context.Assignments
                .Where(a => a.DueDate >= today &&
                            a.DueDate <= next7Days)
                .ToListAsync();

            return Ok(result);
        }

        [HttpGet("13")]
        public async Task<IActionResult> GetCoursesByCreditsAndTitle()
        {
            var result = await _context.Courses
                .OrderByDescending(c => c.Credits)
                .ThenBy(c => c.CourseTitle)
                .ToListAsync();

            return Ok(result);
        }

        [HttpGet("14")]
        public async Task<IActionResult> GetCourseDisplayNames()
        {
            var result = await _context.Courses
                .Select(c => new
                {
                    DisplayName = c.CourseCode + " - " + c.CourseTitle
                })
                .ToListAsync();

            return Ok(result);
        }

        [HttpGet("15")]
        public async Task<IActionResult> GetAssignmentMarks()
        {
            var result = await _context.Assignments
                .Select(a => new
                {
                    a.Title,
                    a.MaxMarks,
                    PassMarks = a.MaxMarks * 0.40
                })
                .ToListAsync();

            return Ok(result);
        }

        [HttpGet("16")]
        public async Task<IActionResult> GetSubmissionStatuses()
        {
            var result = await _context.Submissions
                .Select(s => s.Status)
                .Distinct()
                .OrderBy(s => s)
                .ToListAsync();

            return Ok(result);
        }

        [HttpGet("17")]
        public async Task<IActionResult> GetLateOrUngradedSubmissions()
        {
            var result = await _context.Submissions
                .Where(s => s.Status == "Late" ||
                            s.MarksObtained == null)
                .Select(s => new
                {
                    s.SubmissionId,
                    s.Status,
                    s.MarksObtained
                })
                .ToListAsync();

            return Ok(result);
        }

        [HttpGet("18")]
        public async Task<IActionResult> GetTop5Submissions()
        {
            var result = await _context.Submissions
                .Where(s => s.MarksObtained != null)
                .OrderByDescending(s => s.MarksObtained)
                .Take(5)
                .Select(s => new
                {
                    s.SubmissionId,
                    s.StudentId,
                    s.MarksObtained
                })
                .ToListAsync();

            return Ok(result);
        }

        [HttpGet("19")]
        public async Task<IActionResult> GetRecentCourses()
        {
            var result = await _context.Courses
                .Where(c => c.IsDeleted == false)
                .OrderByDescending(c => c.CreatedAt)
                .Take(3)
                .Select(c => new
                {
                    c.CourseCode,
                    c.CourseTitle,
                    c.CreatedAt
                })
                .ToListAsync();

            return Ok(result);
        }

        [HttpGet("20")]
        public async Task<IActionResult> GetActiveStudentsPage2()
        {
            var result = await (
                from u in _context.Users
                join r in _context.Roles
                    on u.RoleId equals r.RoleId
                where u.IsActive == true
                      && u.IsDeleted == false
                      && r.RoleName == "Student"
                orderby u.FullName
                select u
            )
            .Skip(5)
            .Take(5)
            .ToListAsync();

            return Ok(result);
        }
    }
}
```

---

# Important LINQ Operators for Exam

| Operator | Purpose | Example |
|---|---|---|
| `Select()` | Select/project columns | `Select(c => c.CourseTitle)` |
| `Where()` | Filter records | `Where(c => c.Credits > 3)` |
| `FirstOrDefault()` | First matching record or null | `FirstOrDefault(u => u.Email == email)` |
| `Contains()` | Checks text contains value | `Contains("Programming")` |
| `StartsWith()` | Checks starting text | `StartsWith("2401")` |
| `OrderBy()` | Ascending sort | `OrderBy(c => c.CourseTitle)` |
| `OrderByDescending()` | Descending sort | `OrderByDescending(c => c.Credits)` |
| `ThenBy()` | Second ascending sort | `ThenBy(c => c.CourseTitle)` |
| `Distinct()` | Removes duplicates | `Distinct()` |
| `Take()` | Takes first N records | `Take(5)` |
| `Skip()` | Skips first N records | `Skip(5)` |
| `ToListAsync()` | Executes and returns list | `ToListAsync()` |

---

# Quick Revision

### Projection

```csharp
.Select(c => new
{
    c.CourseCode,
    c.CourseTitle
})
```

### Filtering

```csharp
.Where(c => c.Credits > 3)
```

### Contains

```csharp
.Where(c => c.CourseTitle.Contains("Programming"))
```

### StartsWith

```csharp
.Where(c => c.CourseCode.StartsWith("2401"))
```

### First record

```csharp
.FirstOrDefaultAsync()
```

### Ascending order

```csharp
.OrderBy(c => c.CourseTitle)
```

### Descending order

```csharp
.OrderByDescending(c => c.Credits)
```

### Multiple ordering

```csharp
.OrderByDescending(c => c.Credits)
.ThenBy(c => c.CourseTitle)
```

### Remove duplicates

```csharp
.Distinct()
```

### Top records

```csharp
.Take(5)
```

### Pagination

```csharp
.Skip(5)
.Take(5)
```

---

# Viva Questions

## 1. What is LINQ?

LINQ stands for Language Integrated Query. It is used to query data using C# syntax.

## 2. What is projection?

Projection means selecting only the required fields from the result. `Select()` is commonly used for projection.

## 3. What is the use of Where()?

`Where()` filters records according to a condition.

## 4. Difference between OrderBy() and OrderByDescending()?

- `OrderBy()` → ascending order
- `OrderByDescending()` → descending order

## 5. What is ThenBy()?

`ThenBy()` is used for secondary sorting after `OrderBy()` or `OrderByDescending()`.

## 6. What does FirstOrDefault() return if no record is found?

It returns `null` for reference-type results.

## 7. What is Distinct()?

`Distinct()` removes duplicate values.

## 8. What is Take(5)?

It returns at most 5 records.

## 9. What is Skip(5)?

It skips the first 5 records.

## 10. What is the difference between Select() and Where()?

- `Select()` → chooses what data to return.
- `Where()` → chooses which records to return.

---

# Exam Pattern to Remember

Most LINQ questions can be solved using this pattern:

```csharp
var result = await _context.TableName
    .Where(condition)
    .Select(x => new
    {
        x.Column1,
        x.Column2
    })
    .OrderBy(x => x.Column1)
    .ToListAsync();
```

Not every question needs every operator. Use only the operators required by the question.

