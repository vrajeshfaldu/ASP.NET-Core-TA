# Lab 13 – Pagination, Advanced Filtering and Dynamic Sorting Using LINQ

## Objective

Implement pagination using page number and page size parameters for the Users, Assignment, and Submission modules.

Construct advanced filtering using score range, status (Upcoming / Overdue based on DueDate), course filtering, and dynamic sorting operations using LINQ expressions.

---

# 1. Important Functions / Concepts

## 1.1 AsQueryable()

`AsQueryable()` allows us to build a LINQ query step-by-step.

```csharp
var query = _context.Assignments.AsQueryable();
```

We can add `Where()`, `OrderBy()`, and other conditions before executing the query.

---

## 1.2 Where()

`Where()` is used to filter records.

```csharp
query = query.Where(a => a.CourseId == courseId.Value);
```

It returns only records matching the condition.

---

## 1.3 Skip()

`Skip()` skips a specific number of records.

```csharp
.Skip((pageNumber - 1) * pageSize)
```

Formula:

```text
Skip = (pageNumber - 1) × pageSize
```

Example:

```text
Page 1, size 10 → skip 0
Page 2, size 10 → skip 10
Page 3, size 10 → skip 20
```

---

## 1.4 Take()

`Take()` specifies how many records should be returned.

```csharp
.Take(pageSize)
```

If `pageSize = 10`, 10 records are returned.

---

## 1.5 OrderBy()

`OrderBy()` sorts records in ascending order.

```csharp
query.OrderBy(a => a.DueDate);
```

---

## 1.6 OrderByDescending()

`OrderByDescending()` sorts records in descending order.

```csharp
query.OrderByDescending(a => a.DueDate);
```

---

## 1.7 Nullable Parameters

A parameter such as:

```csharp
int? courseId
```

can contain an integer or `null`.

Use:

```csharp
courseId.HasValue
```

to check whether a value was supplied.

Use:

```csharp
courseId.Value
```

to get the actual value.

---

# Part A – Pagination

## Question 1: Implement pagination for Users

Add pagination to the `GetAll()` method of `UserController`.

```csharp
[HttpGet]
public async Task<IActionResult> GetAll(int pageNumber = 1, int pageSize = 10)
{
    var result = await _context.Users
        .Skip((pageNumber - 1) * pageSize)
        .Take(pageSize)
        .ToListAsync();

    return Ok(result);
}
```

### Explanation

`pageNumber = 1` means the first page.

`pageSize = 10` means 10 users per page.

The main pagination logic is:

```csharp
.Skip((pageNumber - 1) * pageSize)
.Take(pageSize)
```

---

## Question 2: Implement pagination for Assignments

Add pagination to the `GetAll()` method of `AssignmentController`.

```csharp
[HttpGet]
public async Task<IActionResult> GetAll(int pageNumber = 1, int pageSize = 10)
{
    var result = await _context.Assignments
        .Skip((pageNumber - 1) * pageSize)
        .Take(pageSize)
        .ToListAsync();

    return Ok(result);
}
```

---

## Question 3: Implement pagination for Submissions

Add pagination to the `GetAll()` method of `SubmissionController`.

```csharp
[HttpGet]
public async Task<IActionResult> GetAll(int pageNumber = 1, int pageSize = 10)
{
    var result = await _context.Submissions
        .Skip((pageNumber - 1) * pageSize)
        .Take(pageSize)
        .ToListAsync();

    return Ok(result);
}
```

---

# Testing Pagination

### First page

```text
/api/Assignment/GetAll?pageNumber=1&pageSize=5
```

Returns records 1–5.

### Second page

```text
/api/Assignment/GetAll?pageNumber=2&pageSize=5
```

Returns records 6–10.

### Third page

```text
/api/Assignment/GetAll?pageNumber=3&pageSize=5
```

Returns records 11–15.

---

# Part B – Advanced Filtering and Dynamic Sorting

The advanced filtering is implemented in the `AssignmentController`.

The filter supports:

1. Minimum marks
2. Maximum marks
3. Upcoming assignments
4. Overdue assignments
5. Course filtering
6. Sorting by DueDate
7. Sorting by MaxMarks
8. Ascending or descending sorting

> Note: In the CourseHub schema, assignments have `MaxMarks`. Therefore, the score-range filter below uses `MaxMarks`. If your instructor specifically means student `MarksObtained`, the filter should instead be implemented on `Submission`.

---

# Question 4: Advanced Filtering of Assignments

```csharp
[HttpGet("filter")]
public async Task<IActionResult> Filter(
    int? minMarks,
    int? maxMarks,
    string? status,
    int? courseId,
    string? sortBy,
    bool descending = false)
{
    var query = _context.Assignments.AsQueryable();

    if (minMarks.HasValue)
        query = query.Where(a => a.MaxMarks >= minMarks.Value);

    if (maxMarks.HasValue)
        query = query.Where(a => a.MaxMarks <= maxMarks.Value);

    if (courseId.HasValue)
        query = query.Where(a => a.CourseId == courseId.Value);

    if (status == "Upcoming")
        query = query.Where(a => a.DueDate >= DateTime.UtcNow);

    if (status == "Overdue")
        query = query.Where(a => a.DueDate < DateTime.UtcNow);

    if (sortBy == "DueDate")
    {
        query = descending
            ? query.OrderByDescending(a => a.DueDate)
            : query.OrderBy(a => a.DueDate);
    }
    else if (sortBy == "MaxMarks")
    {
        query = descending
            ? query.OrderByDescending(a => a.MaxMarks)
            : query.OrderBy(a => a.MaxMarks);
    }
    else
    {
        query = query.OrderBy(a => a.AssignmentId);
    }

    var result = await query.ToListAsync();

    return Ok(result);
}
```

---

# Explanation of the Filter Method

## Step 1 – Create Query

```csharp
var query = _context.Assignments.AsQueryable();
```

Initially, the query contains all assignments.

The query is built step-by-step and executed at `ToListAsync()`.

---

## Step 2 – Minimum Score

```csharp
if (minMarks.HasValue)
    query = query.Where(a => a.MaxMarks >= minMarks.Value);
```

For:

```text
minMarks=40
```

only assignments with `MaxMarks >= 40` are returned.

---

## Step 3 – Maximum Score

```csharp
if (maxMarks.HasValue)
    query = query.Where(a => a.MaxMarks <= maxMarks.Value);
```

For:

```text
maxMarks=100
```

only assignments with `MaxMarks <= 100` are returned.

---

## Step 4 – Course Filter

```csharp
if (courseId.HasValue)
    query = query.Where(a => a.CourseId == courseId.Value);
```

For:

```text
courseId=1
```

only assignments belonging to Course 1 are returned.

---

## Step 5 – Upcoming Assignments

```csharp
if (status == "Upcoming")
    query = query.Where(a => a.DueDate >= DateTime.UtcNow);
```

An assignment is Upcoming when its due date has not passed.

---

## Step 6 – Overdue Assignments

```csharp
if (status == "Overdue")
    query = query.Where(a => a.DueDate < DateTime.UtcNow);
```

An assignment is Overdue when its due date has already passed.

---

## Step 7 – Sort by DueDate

```csharp
if (sortBy == "DueDate")
{
    query = descending
        ? query.OrderByDescending(a => a.DueDate)
        : query.OrderBy(a => a.DueDate);
}
```

`descending = true` puts the latest due date first.

---

## Step 8 – Sort by MaxMarks

```csharp
else if (sortBy == "MaxMarks")
{
    query = descending
        ? query.OrderByDescending(a => a.MaxMarks)
        : query.OrderBy(a => a.MaxMarks);
}
```

This sorts assignments by maximum marks.

---

## Step 9 – Default Sorting

```csharp
else
{
    query = query.OrderBy(a => a.AssignmentId);
}
```

If no sorting option is supplied, assignments are sorted by `AssignmentId`.

---

# API Testing Examples

## 1. Get second page

```text
/api/Assignment/GetAll?pageNumber=2&pageSize=5
```

## 2. Get upcoming assignments

```text
/api/Assignment/filter?status=Upcoming
```

## 3. Get overdue assignments

```text
/api/Assignment/filter?status=Overdue
```

## 4. Filter by course

```text
/api/Assignment/filter?courseId=1
```

## 5. Filter by score range

```text
/api/Assignment/filter?minMarks=40&maxMarks=100
```

## 6. Sort by DueDate

```text
/api/Assignment/filter?sortBy=DueDate
```

## 7. Sort by DueDate descending

```text
/api/Assignment/filter?sortBy=DueDate&descending=true
```

## 8. Combine multiple filters

```text
/api/Assignment/filter?status=Upcoming&courseId=1&sortBy=DueDate&descending=true
```

This means:

- Only Upcoming assignments
- Course ID = 1
- Sort by DueDate
- Latest due date first

---

# Quick Revision

| Function | Purpose |
|---|---|
| `AsQueryable()` | Build a query dynamically |
| `Where()` | Filter records |
| `Skip()` | Skip records for pagination |
| `Take()` | Select page size |
| `OrderBy()` | Ascending sorting |
| `OrderByDescending()` | Descending sorting |
| `HasValue` | Check nullable parameter |
| `Value` | Get value from nullable parameter |
| `ToListAsync()` | Execute query and return list |

---

# Pagination Formula

```text
Skip = (pageNumber - 1) × pageSize
Take = pageSize
```

Example:

```text
pageNumber = 2
pageSize = 10

Skip = (2 - 1) × 10
     = 10

Take = 10
```

Therefore, page 2 returns the next 10 records.

---

# Viva Questions

### 1. What is pagination?

Pagination divides a large number of records into smaller pages.

### 2. Why do we use Skip()?

`Skip()` skips records before the required page.

### 3. Why do we use Take()?

`Take()` limits the number of records returned.

### 4. What is AsQueryable()?

It allows us to build a LINQ query dynamically before executing it.

### 5. Difference between OrderBy() and OrderByDescending()?

`OrderBy()` sorts in ascending order, while `OrderByDescending()` sorts in descending order.

### 6. Why are minMarks and maxMarks nullable?

Because the user may provide only one, both, or neither of the filter values.

### 7. What does HasValue do?

It checks whether a nullable parameter contains a value.

### 8. What is dynamic sorting?

Dynamic sorting means the sorting field and direction are selected based on API parameters.

### 9. What is an Upcoming assignment?

An assignment whose `DueDate` is greater than or equal to the current UTC date/time.

### 10. What is an Overdue assignment?

An assignment whose `DueDate` is earlier than the current UTC date/time.
