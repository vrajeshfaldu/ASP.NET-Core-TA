# Lab 5 — ASP.NET Core Fundamentals

## A. Basic Concepts

### 1. ASP.NET Core Web API

ASP.NET Core Web API is used to create HTTP services that can return data to applications such as browsers or mobile applications.

### 2. Controller

A controller handles incoming HTTP requests and returns an HTTP response.

Example:

```csharp
[ApiController]
[Route("api/[controller]")]
public class ProductsController : ControllerBase
{
}
```

- `[ApiController]` enables API-specific behavior.
- `[Route("api/[controller]")]` defines the route.
- `ControllerBase` provides helper methods such as `Ok()` and `NotFound()`.

### 3. HTTP Verbs

| Verb | Common Purpose |
|---|---|
| `GET` | Retrieve data |
| `POST` | Create data |
| `PUT` | Update data |
| `DELETE` | Remove data |

### 4. ActionResult

`ActionResult<T>` allows a controller to return:

- A typed object
- `Ok()`
- `NotFound()`
- Other HTTP responses

---

## B. Questions

### Question 1: Create a Book model

Create a `Book` model with:

- `Id`
- `Title`
- `Author`
- `Price`

```csharp
namespace lab5.Models
{
    public class Book
    {
        public int Id { get; set; }

        public string Title { get; set; } = string.Empty;

        public string Author { get; set; } = string.Empty;

        public decimal Price { get; set; }
    }
}
```

**Explanation:**

- `Id` uniquely identifies a book.
- `Title` stores the book title.
- `Author` stores the author's name.
- `decimal` is suitable for storing currency values.

---

### Question 2: Create five sample records

Create a static list in `BooksController`:

```csharp
private static readonly List<Book> Books = new()
{
    new Book
    {
        Id = 1,
        Title = "C# Programming",
        Author = "John Smith",
        Price = 500
    },
    new Book
    {
        Id = 2,
        Title = "ASP.NET Core",
        Author = "David Brown",
        Price = 650
    },
    new Book
    {
        Id = 3,
        Title = "Database Systems",
        Author = "Maria Garcia",
        Price = 575
    },
    new Book
    {
        Id = 4,
        Title = "Data Structures",
        Author = "Robert Wilson",
        Price = 475
    },
    new Book
    {
        Id = 5,
        Title = "Cloud Computing",
        Author = "Sarah Johnson",
        Price = 700
    }
};
```

**Explanation:**

- `static` means only one copy of the collection is created.
- `readonly` prevents the collection variable itself from being reassigned.
- `new()` creates a list of `Book` objects.

---

### Question 3: Return all books

Create `Controllers/BooksController.cs` and add:

```csharp
using lab5.Models;
using Microsoft.AspNetCore.Mvc;

namespace lab5.Controllers
{
    [ApiController]
    [Route("api/[controller]")]
    public class BooksController : ControllerBase
    {
        private static readonly List<Book> Books = new()
        {
            new Book
            {
                Id = 1,
                Title = "C# Programming",
                Author = "John Smith",
                Price = 500
            },
            new Book
            {
                Id = 2,
                Title = "ASP.NET Core",
                Author = "David Brown",
                Price = 650
            },
            new Book
            {
                Id = 3,
                Title = "Database Systems",
                Author = "Maria Garcia",
                Price = 575
            },
            new Book
            {
                Id = 4,
                Title = "Data Structures",
                Author = "Robert Wilson",
                Price = 475
            },
            new Book
            {
                Id = 5,
                Title = "Cloud Computing",
                Author = "Sarah Johnson",
                Price = 700
            }
        };

        [HttpGet]
        public ActionResult<List<Book>> GetAllBooks()
        {
            return Ok(Books);
        }
    }
}
```

**Endpoint:**

```text
GET /api/books
```

**Response:**

```json
[
  {
    "id": 1,
    "title": "C# Programming",
    "author": "John Smith",
    "price": 500
  }
]
```

---

### Question 4: Find a book by ID

Add the following action to `BooksController`:

```csharp
[HttpGet("{id:int}")]
public ActionResult<Book> GetBookById(int id)
{
    Book? book = Books
        .FirstOrDefault(book => book.Id == id);

    if (book == null)
    {
        return NotFound($"Book with ID {id} was not found.");
    }

    return Ok(book);
}
```

**Endpoint:**

```text
GET /api/books/2
```

**Explanation:**

- `{id:int}` accepts an integer from the URL.
- `FirstOrDefault()` returns the first matching book or `null`.
- `NotFound()` returns HTTP status `404`.
- `Ok()` returns HTTP status `200`.

---

### Question 5: Search books by title

Add the following action:

```csharp
[HttpGet("search")]
public ActionResult<List<Book>> SearchByTitle(string title)
{
    List<Book> result = Books
        .Where(book =>
            book.Title.Contains(title, StringComparison.OrdinalIgnoreCase))
        .ToList();

    return Ok(result);
}
```

**Endpoint:**

```text
GET /api/books/search?title=core
```

**Explanation:**

- `Where()` filters the list.
- `Contains()` checks for matching text.
- `OrdinalIgnoreCase` makes the search case-insensitive.
- `ToList()` converts the filtered result into a list.

---

### Question 6: Return books with prices above a value

Add the following action:

```csharp
[HttpGet("price/{minimumPrice:decimal}")]
public ActionResult<List<Book>> GetExpensiveBooks(decimal minimumPrice)
{
    List<Book> result = Books
        .Where(book => book.Price >= minimumPrice)
        .ToList();

    return Ok(result);
}
```

**Endpoint:**

```text
GET /api/books/price/500
```

**Explanation:**

- The route constraint `decimal` validates the parameter type.
- `>=` includes books with the exact minimum price.
- The result contains only books satisfying the condition.

---

## C. Complete Controller

```csharp
using lab5.Models;
using Microsoft.AspNetCore.Mvc;

namespace lab5.Controllers
{
    [ApiController]
    [Route("api/[controller]")]
    public class BooksController : ControllerBase
    {
        private static readonly List<Book> Books = new()
        {
            new Book
            {
                Id = 1,
                Title = "C# Programming",
                Author = "John Smith",
                Price = 500
            },
            new Book
            {
                Id = 2,
                Title = "ASP.NET Core",
                Author = "David Brown",
                Price = 650
            },
            new Book
            {
                Id = 3,
                Title = "Database Systems",
                Author = "Maria Garcia",
                Price = 575
            },
            new Book
            {
                Id = 4,
                Title = "Data Structures",
                Author = "Robert Wilson",
                Price = 475
            },
            new Book
            {
                Id = 5,
                Title = "Cloud Computing",
                Author = "Sarah Johnson",
                Price = 700
            }
        };

        [HttpGet]
        public ActionResult<List<Book>> GetAllBooks()
        {
            return Ok(Books);
        }

        [HttpGet("{id:int}")]
        public ActionResult<Book> GetBookById(int id)
        {
            Book? book = Books
                .FirstOrDefault(book => book.Id == id);

            if (book == null)
            {
                return NotFound($"Book with ID {id} was not found.");
            }

            return Ok(book);
        }

        [HttpGet("search")]
        public ActionResult<List<Book>> SearchByTitle(string title)
        {
            List<Book> result = Books
                .Where(book =>
                    book.Title.Contains(title, StringComparison.OrdinalIgnoreCase))
                .ToList();

            return Ok(result);
        }

        [HttpGet("price/{minimumPrice:decimal}")]
        public ActionResult<List<Book>> GetExpensiveBooks(decimal minimumPrice)
        {
            List<Book> result = Books
                .Where(book => book.Price >= minimumPrice)
                .ToList();

            return Ok(result);
        }
    }
}
```

---

## D. Complete Endpoint Summary

| Purpose | Endpoint |
|---|---|
| Get all books | `GET /api/books` |
| Get book by ID | `GET /api/books/2` |
| Search book title | `GET /api/books/search?title=core` |
| Filter by minimum price | `GET /api/books/price/500` |
