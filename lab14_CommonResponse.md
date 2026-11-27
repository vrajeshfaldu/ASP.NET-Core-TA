# Lab 14 – Common Response Wrappers

## Objective

To design a **common response wrapper** for ASP.NET Core Web API responses containing:

- Status
- Message
- Success flag
- Response payload

This provides a consistent structure for all API responses.

---

## 1. Create Common Response Wrapper

Create a `Helper` folder and add `ApiResponse.cs`.

### ApiResponse.cs

```csharp
namespace lab6.Helper;

public class ApiResponse<T>
{
    public int Status { get; set; }

    public string Message { get; set; } = string.Empty;

    public bool Success { get; set; }

    public T? Data { get; set; }
}
```

### Properties

| Property | Purpose |
|---|---|
| `Status` | HTTP status code such as 200, 201, 404 |
| `Message` | Description of the API result |
| `Success` | Indicates whether the operation was successful |
| `Data` | Actual response payload |

The `T` makes the response wrapper **generic**, so it can contain different types of data.

---

## 2. Successful Response Example

For example, when getting users:

```csharp
var response = new ApiResponse<List<User>>
{
    Status = 200,
    Message = "Users retrieved successfully.",
    Success = true,
    Data = users
};

return Ok(response);
```

The response structure will be:

```json
{
  "status": 200,
  "message": "Users retrieved successfully.",
  "success": true,
  "data": [
    {
      "userId": 1,
      "fullName": "John",
      "email": "john@example.com"
    }
  ]
}
```

---

## 3. Error Response Example

If a user is not found:

```csharp
var response = new ApiResponse<User>
{
    Status = 404,
    Message = "User not found.",
    Success = false,
    Data = null
};

return NotFound(response);
```

Response:

```json
{
  "status": 404,
  "message": "User not found.",
  "success": false,
  "data": null
}
```

---

## 4. Create Response Example

For a successful POST request:

```csharp
var response = new ApiResponse<User>
{
    Status = 201,
    Message = "User created successfully.",
    Success = true,
    Data = user
};

return StatusCode(201, response);
```

Response:

```json
{
  "status": 201,
  "message": "User created successfully.",
  "success": true,
  "data": {
    "userId": 1,
    "fullName": "John",
    "email": "john@example.com"
  }
}
```

---

## 5. Generic Response

The `ApiResponse<T>` class can be used with different data types.

### Single User

```csharp
ApiResponse<User>
```

### List of Users

```csharp
ApiResponse<List<User>>
```

### Single Role

```csharp
ApiResponse<Role>
```

### List of Roles

```csharp
ApiResponse<List<Role>>
```

### No Data

```csharp
ApiResponse<object>
```

---

## 6. Controller Example

Example using `UsersController`:

```csharp
[HttpGet("{id}")]
public async Task<IActionResult> GetById(int id)
{
    var user = await _context.Users.FindAsync(id);

    if (user == null)
    {
        var response = new ApiResponse<User>
        {
            Status = 404,
            Message = "User not found.",
            Success = false,
            Data = null
        };

        return NotFound(response);
    }

    var successResponse = new ApiResponse<User>
    {
        Status = 200,
        Message = "User retrieved successfully.",
        Success = true,
        Data = user
    };

    return Ok(successResponse);
}
```

---

## 7. Advantages

Using a common response wrapper provides:

- Consistent API response structure
- Easy error handling
- Clear success/failure information
- Standard status information
- Generic payload support
- Cleaner frontend/API integration

---

## Response Structure

Every API response follows the same basic structure:

```text
ApiResponse<T>
│
├── Status
├── Message
├── Success
└── Data
```

Example:

```text
{
    Status: 200,
    Message: "Operation successful",
    Success: true,
    Data: <response payload>
}
```

---

## Conclusion

The `ApiResponse<T>` class provides a **common response wrapper** for the Web API.

It standardizes every response using:

**Status + Message + Success + Data**

The generic `<T>` allows the same wrapper to be used for users, roles, courses, assignments, submissions, lists, and other response payloads.
