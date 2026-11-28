# Lab 16 – Model Binding using Body, Query, Route, Header, and Form

## Objective

To implement model binding in ASP.NET Core Web API using:

- Request body
- Query parameters
- Route parameters
- Header parameters
- Form-data
- Optional parameters
- Custom request validation

---

# Part A

## Implement model binding using request body, query parameters, and route parameters for API operations.

### 1. Request Body Binding

Request body binding is used to receive JSON data from the client.

### Example

```csharp
[HttpPost]
public IActionResult CreateUser(
    [FromBody] UserRequestDto dto)
{
    return Ok(dto);
}
```

### Request

```http
POST /api/Users
Content-Type: application/json
```

```json
{
    "roleId": 3,
    "fullName": "John",
    "email": "john@example.com",
    "passwordHash": "12345"
}
```

Here, the JSON request body is bound to `UserRequestDto`.

---

## 2. Query Parameter Binding

Query parameters are values passed after `?` in the URL.

### Example

```csharp
[HttpGet("search")]
public IActionResult Search(
    [FromQuery] string name,
    [FromQuery] int pageNumber = 1,
    [FromQuery] int pageSize = 10)
{
    return Ok(new
    {
        Name = name,
        PageNumber = pageNumber,
        PageSize = pageSize
    });
}
```

### Request

```text
GET /api/Users/search?name=John&pageNumber=2&pageSize=10
```

The values are:

```text
name       → John
pageNumber → 2
pageSize   → 10
```

---

## 3. Route Parameter Binding

Route parameters are values passed as part of the URL.

### Example

```csharp
[HttpGet("{id}")]
public IActionResult GetById(
    [FromRoute] int id)
{
    return Ok(new
    {
        Id = id
    });
}
```

### Request

```text
GET /api/Users/5
```

Here:

```text
5 → id
```

The value `5` is automatically bound to the `id` parameter.

---

# Part B

## Integrate header binding, form-data handling, optional parameters, and custom request validation mechanisms for complex API requests.

---

## 4. Header Binding

Headers contain additional information sent with an HTTP request.

The `[FromHeader]` attribute is used to bind a header value.

### Example

```csharp
[HttpGet("header")]
public IActionResult GetHeader(
    [FromHeader(Name = "X-Api-Key")] string apiKey)
{
    return Ok(new
    {
        ApiKey = apiKey
    });
}
```

### Request Header

```text
X-Api-Key: ABC123
```

The value `ABC123` is bound to the `apiKey` parameter.

---

## 5. Form-Data Handling

Form-data is commonly used when uploading files.

The `[FromForm]` attribute is used to receive form-data.

### Example

```csharp
[HttpPost("upload")]
public IActionResult Upload(
    [FromForm] string fileName,
    [FromForm] IFormFile file)
{
    return Ok(new
    {
        FileName = fileName,
        FileSize = file.Length
    });
}
```

The request uses:

```text
Content-Type: multipart/form-data
```

Example form-data:

```text
fileName → test.pdf
file     → selected file
```

---

## 6. Optional Parameters

Optional parameters allow the client to omit a value.

### Example

```csharp
[HttpGet("users")]
public IActionResult GetUsers(
    int pageNumber = 1,
    int pageSize = 10,
    string? search = null)
{
    return Ok(new
    {
        PageNumber = pageNumber,
        PageSize = pageSize,
        Search = search
    });
}
```

The client can call:

```text
GET /api/Users/users
```

Default values will be used:

```text
pageNumber = 1
pageSize   = 10
search     = null
```

Or the client can provide values:

```text
GET /api/Users/users?pageNumber=2&pageSize=20&search=John
```

---

## 7. Multiple Binding Sources

Different binding sources can be used in the same API operation.

### Example

```csharp
[HttpPut("{id}")]
public IActionResult UpdateUser(
    [FromRoute] int id,
    [FromQuery] bool notify,
    [FromBody] UserRequestDto dto)
{
    return Ok(new
    {
        Id = id,
        Notify = notify,
        User = dto
    });
}
```

### Request

```text
PUT /api/Users/5?notify=true
```

Request body:

```json
{
    "roleId": 2,
    "fullName": "John",
    "email": "john@example.com",
    "passwordHash": "12345"
}
```

Here:

```text
5           → Route parameter
true        → Query parameter
JSON object → Body parameter
```

---

# 8. Custom Request Validation

Custom validation can be performed before processing the request.

### Example

```csharp
[HttpPost("validate")]
public IActionResult ValidateUser(
    [FromBody] UserRequestDto dto)
{
    if (string.IsNullOrWhiteSpace(dto.FullName))
    {
        return BadRequest(new
        {
            Message = "Full name is required."
        });
    }

    if (string.IsNullOrWhiteSpace(dto.Email))
    {
        return BadRequest(new
        {
            Message = "Email is required."
        });
    }

    if (!dto.Email.Contains("@"))
    {
        return BadRequest(new
        {
            Message = "Invalid email address."
        });
    }

    return Ok(new
    {
        Message = "Request is valid.",
        Data = dto
    });
}
```

The API checks the request before performing the actual operation.

---

# 9. Validation with Common Response Wrapper

Since the project already uses `ApiResponse<T>`, validation errors can use the same response structure.

```csharp
if (string.IsNullOrWhiteSpace(dto.FullName))
{
    var response = new ApiResponse<object>
    {
        Status = 400,
        Message = "Full name is required.",
        Success = false,
        Data = null
    };

    return BadRequest(response);
}
```

Response:

```json
{
    "status": 400,
    "message": "Full name is required.",
    "success": false,
    "data": null
}
```

---

# Summary

| Binding Type | Attribute | Example |
|---|---|---|
| Request Body | `[FromBody]` | JSON data |
| Query Parameter | `[FromQuery]` | `?pageNumber=2` |
| Route Parameter | `[FromRoute]` | `/Users/5` |
| Header | `[FromHeader]` | `X-Api-Key` |
| Form-data | `[FromForm]` | File upload |

## Conclusion

In this lab, model binding was implemented using different parts of an HTTP request.

### Part A

- Request body binding
- Query parameter binding
- Route parameter binding

### Part B

- Header binding
- Form-data handling
- Optional parameters
- Multiple binding sources
- Custom request validation
- Common response wrapper for validation errors

Model binding allows ASP.NET Core Web API to automatically receive and convert HTTP request data into C# parameters and objects.
