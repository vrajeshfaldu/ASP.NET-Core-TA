# Lab 12 – LINQ Aggregate, Grouping, Any, All and Join

## Aim

To use LINQ with Entity Framework Core for:
- Aggregate functions
- Grouping
- Conditional checks
- Joins
- Projection
- Related tables

---

# 1. Important LINQ Functions

## Count()

Counts records.

```csharp
var count = await _context.Courses.CountAsync();
```

With a condition:

```csharp
var count = await _context.Courses
    .CountAsync(c => c.IsDeleted == false);
```

`CountAsync()` returns the number of matching records.

---

## Sum()

Adds values of a numeric column.

```csharp
var total = await _context.Courses
    .SumAsync(c => c.Credits);
```

---

## Max()

Finds the highest value.

```csharp
var highest = await _context.Assignments
    .MaxAsync(a => a.MaxMarks);
```

---

## Min()

Finds the lowest value.

```csharp
var lowest = await _context.Assignments
    .MinAsync(a => a.MaxMarks);
```

---

## Average()

Calculates the average.

```csharp
var average = await _context.Submissions
    .Where(s => s.MarksObtained != null)
    .AverageAsync(s => s.MarksObtained);
```

If there may be no records, use:

```csharp
var average = await _context.Submissions
    .Where(s => s.Status == "Graded" &&
                s.MarksObtained != null)
    .Select(s => (double?)s.MarksObtained)
    .AverageAsync() ?? 0;
```

`?? 0` means return `0` if the average is null.

---

## GroupBy()

Groups records with the same value.

```csharp
var result = await _context.Courses
    .GroupBy(c => c.Credits)
    .Select(g => new
    {
        Credits = g.Key,
        Count = g.Count()
    })
    .ToListAsync();
```

- `g.Key` = value used for grouping
- `g.Count()` = number of records in that group

---

## Any()

Checks whether at least one record satisfies a condition.

```csharp
var result = await _context.Assignments
    .AnyAsync(a => a.DueDate < DateTime.UtcNow);
```

Returns `true` or `false`.

---

## All()

Checks whether every record satisfies a condition.

```csharp
var result = await _context.Courses
    .AllAsync(c => c.Credits >= 1);
```

Returns `true` or `false`.

---

## Join

Combines related records from two tables.

```csharp
from u in _context.Users
join r in _context.Roles
    on u.RoleId equals r.RoleId
select new
{
    u.FullName,
    r.RoleName
}
```

---

## Select()

Projects only the required fields.

```csharp
.Select(c => new
{
    c.CourseTitle,
    c.CourseCode
})
```

---

## Where()

Filters records.

```csharp
.Where(s => s.Status == "Graded")
```

---

# Questions and Queries

## Q1. Count the total number of courses that are not deleted.

```csharp
var result = await _context.Courses
    .CountAsync(c => c.IsDeleted == false);

return Ok(result);
```

**Explanation:** Counts only courses where `IsDeleted` is false.

---

## Q2. Count the number of submissions with status Late.

```csharp
var result = await _context.Submissions
    .CountAsync(s => s.Status == "Late");

return Ok(result);
```

**Explanation:** Counts submissions whose status is `Late`.

---

## Q3. Find the total number of credits across all courses.

```csharp
var result = await _context.Courses
    .SumAsync(c => c.Credits);

return Ok(result);
```

**Explanation:** `SumAsync()` adds all course credits.

---

## Q4. Find the highest and lowest MaxMarks among all assignments.

```csharp
var highest = await _context.Assignments
    .MaxAsync(a => a.MaxMarks);

var lowest = await _context.Assignments
    .MinAsync(a => a.MaxMarks);

return Ok(new
{
    HighestMarks = highest,
    LowestMarks = lowest
});
```

**Explanation:** `MaxAsync()` finds the highest mark and `MinAsync()` finds the lowest.

---

## Q5. Find the earliest and latest DueDate among assignments of one given course.

Assume `courseId` is provided.

```csharp
var assignments = _context.Assignments
    .Where(a => a.CourseId == courseId);

var earliest = await assignments
    .MinAsync(a => a.DueDate);

var latest = await assignments
    .MaxAsync(a => a.DueDate);

return Ok(new
{
    EarliestDueDate = earliest,
    LatestDueDate = latest
});
```

**Explanation:** First filter assignments by `CourseId`, then find minimum and maximum dates.

---

## Q6. Count the number of courses for each Credits value.

```csharp
var result = await _context.Courses
    .GroupBy(c => c.Credits)
    .Select(g => new
    {
        Credits = g.Key,
        Count = g.Count()
    })
    .ToListAsync();

return Ok(result);
```

**Explanation:** Courses are grouped by credits and each group is counted.

---

## Q7. Count the number of users for each role, showing RoleId and count.

```csharp
var result = await _context.Users
    .GroupBy(u => u.RoleId)
    .Select(g => new
    {
        RoleId = g.Key,
        Count = g.Count()
    })
    .ToListAsync();

return Ok(result);
```

---

## Q8. Count the number of submissions for each Status.

```csharp
var result = await _context.Submissions
    .GroupBy(s => s.Status)
    .Select(g => new
    {
        Status = g.Key,
        Count = g.Count()
    })
    .ToListAsync();

return Ok(result);
```

Example:

```text
Submitted = 10
Graded    = 7
Late      = 2
Pending   = 5
```

---

## Q9. Find the number of assignments for each course, showing CourseId and count.

```csharp
var result = await _context.Assignments
    .GroupBy(a => a.CourseId)
    .Select(g => new
    {
        CourseId = g.Key,
        Count = g.Count()
    })
    .ToListAsync();

return Ok(result);
```

---

## Q10. Retrieve each user's full name together with their role name.

```csharp
var result = await (
    from u in _context.Users
    join r in _context.Roles
        on u.RoleId equals r.RoleId
    select new
    {
        u.FullName,
        r.RoleName
    }
).ToListAsync();

return Ok(result);
```

**Explanation:** `Users` and `Roles` are joined using `RoleId`.

---

## Q11. Find the average MarksObtained of all graded submissions.

```csharp
var result = await _context.Submissions
    .Where(s => s.Status == "Graded" &&
                s.MarksObtained != null)
    .Select(s => (double?)s.MarksObtained)
    .AverageAsync() ?? 0;

return Ok(result);
```

**Explanation:** Only graded submissions with marks are considered. `?? 0` handles the case where no graded submission exists.

---

## Q12. Check whether at least one assignment is past its due date, and whether all courses have credits of 1 or more.

```csharp
var hasPastAssignment = await _context.Assignments
    .AnyAsync(a => a.DueDate < DateTime.UtcNow);

var allCoursesValid = await _context.Courses
    .AllAsync(c => c.Credits >= 1);

return Ok(new
{
    HasPastAssignment = hasPastAssignment,
    AllCoursesValidCredits = allCoursesValid
});
```

**Explanation:**
- `AnyAsync()` checks at least one assignment.
- `AllAsync()` checks every course.

---

## Q13. Find the average MarksObtained for each assignment, considering graded submissions only.

```csharp
var result = await _context.Submissions
    .Where(s => s.Status == "Graded" &&
                s.MarksObtained != null)
    .GroupBy(s => s.AssignmentId)
    .Select(g => new
    {
        AssignmentId = g.Key,
        AverageMarks = g.Average(s => s.MarksObtained)
    })
    .ToListAsync();

return Ok(result);
```

**Explanation:** Filter graded submissions, group by assignment, then calculate average marks.

---

## Q14. Find the courses that have more than 2 assignments.

```csharp
var result = await _context.Assignments
    .GroupBy(a => a.CourseId)
    .Where(g => g.Count() > 2)
    .Select(g => new
    {
        CourseId = g.Key,
        AssignmentCount = g.Count()
    })
    .ToListAsync();

return Ok(result);
```

**Explanation:** Groups assignments by course and keeps groups with more than 2 assignments.

---

## Q15. Retrieve each course's title together with the full name of its faculty, ordered by faculty name.

```csharp
var result = await (
    from c in _context.Courses
    join u in _context.Users
        on c.FacultyId equals u.UserId
    orderby u.FullName
    select new
    {
        c.CourseTitle,
        FacultyName = u.FullName
    }
).ToListAsync();

return Ok(result);
```

**Explanation:** Courses are joined with Users through `FacultyId`, then ordered by faculty name.

---

## Q16. Retrieve each assignment's title with course title, course code and faculty full name.

```csharp
var result = await (
    from a in _context.Assignments
    join c in _context.Courses
        on a.CourseId equals c.CourseId
    join u in _context.Users
        on c.FacultyId equals u.UserId
    select new
    {
        AssignmentTitle = a.Title,
        CourseTitle = c.CourseTitle,
        c.CourseCode,
        FacultyName = u.FullName
    }
).ToListAsync();

return Ok(result);
```

**Explanation:** Three tables are connected:

```text
Assignments → Courses → Users
```

---

## Q17. For each assignment, return total submissions, graded count and highest marks.

```csharp
var result = await _context.Assignments
    .Select(a => new
    {
        AssignmentId = a.AssignmentId,
        AssignmentTitle = a.Title,

        TotalSubmissions = _context.Submissions
            .Count(s => s.AssignmentId == a.AssignmentId),

        GradedCount = _context.Submissions
            .Count(s => s.AssignmentId == a.AssignmentId &&
                        s.Status == "Graded"),

        HighestMarks = _context.Submissions
            .Where(s => s.AssignmentId == a.AssignmentId &&
                        s.MarksObtained != null)
            .Select(s => s.MarksObtained)
            .Max()
    })
    .ToListAsync();

return Ok(result);
```

**Explanation:**
- `TotalSubmissions` → all submissions
- `GradedCount` → only graded submissions
- `HighestMarks` → highest non-null mark

---

## Q18. Retrieve every course with its assignment titles, including courses that have no assignments.

```csharp
var result = await _context.Courses
    .Select(c => new
    {
        c.CourseId,
        c.CourseTitle,

        AssignmentTitles = _context.Assignments
            .Where(a => a.CourseId == c.CourseId)
            .Select(a => a.Title)
            .ToList()
    })
    .ToListAsync();

return Ok(result);
```

**Explanation:** The query starts from `Courses`, so courses with no assignments are also returned. Their assignment list is empty.

---

## Q19. Retrieve the student name, assignment title, course title and marks obtained for every graded submission.

```csharp
var result = await (
    from s in _context.Submissions
    join u in _context.Users
        on s.StudentId equals u.UserId
    join a in _context.Assignments
        on s.AssignmentId equals a.AssignmentId
    join c in _context.Courses
        on a.CourseId equals c.CourseId
    where s.Status == "Graded"
    select new
    {
        StudentName = u.FullName,
        AssignmentTitle = a.Title,
        CourseTitle = c.CourseTitle,
        s.MarksObtained
    }
).ToListAsync();

return Ok(result);
```

**Explanation:**

```text
Users
  ↓ StudentId
Submissions
  ↓ AssignmentId
Assignments
  ↓ CourseId
Courses
```

Only graded submissions are returned.

---

## Q20. For each faculty member, show the number of courses they own and total submissions received across those courses. Include faculty with zero courses.

```csharp
var result = await _context.Users
    .Where(u => u.RoleId == 2)
    .Select(u => new
    {
        FacultyName = u.FullName,

        CourseCount = _context.Courses
            .Count(c => c.FacultyId == u.UserId),

        TotalSubmissions = _context.Submissions
            .Count(s => _context.Assignments
                .Any(a => a.AssignmentId == s.AssignmentId &&
                    _context.Courses
                        .Any(c => c.CourseId == a.CourseId &&
                                  c.FacultyId == u.UserId)))
    })
    .ToListAsync();

return Ok(result);
```

**Explanation:** The query starts from faculty users, so a faculty member with zero courses is still included.

---

# Quick Revision

| Function | Meaning |
|---|---|
| `Count()` | Counts records |
| `Sum()` | Adds values |
| `Max()` | Highest value |
| `Min()` | Lowest value |
| `Average()` | Average value |
| `GroupBy()` | Creates groups |
| `Any()` | At least one matches |
| `All()` | Every record matches |
| `Join()` | Combines related tables |
| `Select()` | Selects/project fields |
| `Where()` | Filters records |

## Most Important Patterns

### Count

```csharp
CountAsync(x => condition)
```

### Sum

```csharp
SumAsync(x => x.Value)
```

### Max / Min

```csharp
MaxAsync(x => x.Value)
MinAsync(x => x.Value)
```

### GroupBy + Count

```csharp
.GroupBy(x => x.Value)
.Select(g => new
{
    Value = g.Key,
    Count = g.Count()
})
```

### Any

```csharp
.AnyAsync(x => condition)
```

### All

```csharp
.AllAsync(x => condition)
```

### Join

```csharp
join r in _context.Roles
    on u.RoleId equals r.RoleId
```

---

# Viva Questions

### 1. What is an aggregate function?

An aggregate function performs a calculation on multiple records.

Examples: `Count`, `Sum`, `Max`, `Min`, and `Average`.

### 2. What does GroupBy() do?

It groups records having the same value.

### 3. What is g.Key?

`g.Key` is the value used for grouping.

### 4. Difference between Count() and Sum()?

`Count()` counts records, while `Sum()` adds numeric values.

### 5. What does Any() return?

`true` if at least one record satisfies the condition.

### 6. What does All() return?

`true` only when every record satisfies the condition.

### 7. Why is Join used?

Join combines related data from different tables.

### 8. Why use Select()?

Select is used to return only the required fields.

### 9. Why use `(double?)` with Average?

It allows the average value to be null when there are no matching records.

### 10. Difference between Where() and GroupBy()?

`Where()` filters records, while `GroupBy()` creates groups of records.
