# Lab 15 – DTO and AutoMapper

## Objective

To use **DTOs (Data Transfer Objects)** and **AutoMapper** in an ASP.NET Core Web API.

DTOs are used to transfer only the required data between the client and API instead of directly exposing entity models.

---

## 1. Create DTO

Create a `DTOs` folder and add a request DTO and response DTO.

### Example: User DTO

**UserRequestDto.cs**

```csharp
namespace lab6.DTOs
{
    public class UserRequestDto
    {
        public int RoleId { get; set; }
        public string FullName { get; set; } = string.Empty;
        public string Email { get; set; } = string.Empty;
        public string PasswordHash { get; set; } = string.Empty;
    }
}
```

**UserResponseDto.cs**

```csharp
namespace lab6.DTOs
{
    public class UserResponseDto
    {
        public int UserId { get; set; }
        public string FullName { get; set; } = string.Empty;
        public string Email { get; set; } = string.Empty;
        public string RoleName { get; set; } = string.Empty;
        public bool IsActive { get; set; }
        public DateTime CreatedAt { get; set; }
    }
}
```

---

## 2. Create AutoMapper Profile

Create a `Mapping` folder and add `MappingProfile.cs`.

```csharp
using AutoMapper;
using lab6.DTOs;
using lab6.Models;

namespace lab6.Mapping
{
    public class MappingProfile : Profile
    {
        public MappingProfile()
        {
            CreateMap<UserRequestDto, User>();

            CreateMap<User, UserResponseDto>()
                .ForMember(
                    dest => dest.RoleName,
                    opt => opt.MapFrom(
                        src => src.Role.RoleName));
        }
    }
}
```

Here:

- `UserRequestDto → User` is used when receiving data.
- `User → UserResponseDto` is used when sending data.
- `RoleName` is taken from the related `Role` entity.

---

## 3. Register AutoMapper

In `Program.cs`:

```csharp
using lab6.Mapping;

builder.Services.AddAutoMapper(typeof(MappingProfile));
```

---

## 4. Controller Example

Example using `UsersController`:

```csharp
using AutoMapper;
using lab6.Data;
using lab6.DTOs;
using lab6.Helpers;
using lab6.Models;
using Microsoft.AspNetCore.Mvc;
using Microsoft.EntityFrameworkCore;

namespace lab6.Controllers
{
    [Route("api/[controller]")]
    [ApiController]
    public class UsersController : ControllerBase
    {
        private readonly AppDbContext _context;
        private readonly IMapper _mapper;

        public UsersController(
            AppDbContext context,
            IMapper mapper)
        {
            _context = context;
            _mapper = mapper;
        }

        [HttpGet]
        public async Task<IActionResult> GetAll()
        {
            var users = await _context.Users
                .Include(u => u.Role)
                .ToListAsync();

            var data =
                _mapper.Map<List<UserResponseDto>>(users);

            var response =
                new ApiResponse<List<UserResponseDto>>
                {
                    Status = 200,
                    Message = "Users retrieved successfully.",
                    Success = true,
                    Data = data
                };

            return Ok(response);
        }

        [HttpPost]
        public async Task<IActionResult> Post(
            UserRequestDto dto)
        {
            var user =
                _mapper.Map<User>(dto);

            _context.Users.Add(user);
            await _context.SaveChangesAsync();

            var data =
                _mapper.Map<UserResponseDto>(user);

            var response =
                new ApiResponse<UserResponseDto>
                {
                    Status = 201,
                    Message = "User created successfully.",
                    Success = true,
                    Data = data
                };

            return StatusCode(201, response);
        }
    }
}
```

---

## 5. Why DTOs are Used

Instead of:

```text
Client → User Entity → Database
```

we use:

```text
Client → Request DTO → Entity → Database
```

and:

```text
Database → Entity → Response DTO → Client
```

This provides:

- Better security
- Control over exposed fields
- Separation between API and database models
- Cleaner API responses
- Easier maintenance

---

## 6. Important Note

DTOs do **not** need to duplicate all the validation constraints from the entity unless the requirement specifically asks for DTO validation.

For example, if `[Required]` is already present in the `User` model, it can remain there.

The main purpose of this lab is:

**DTO + AutoMapper + Controller integration.**

The same approach can then be applied to:

- Role
- User
- Course
- Assignment
- Submission

The LINQ controllers do not need to be changed for this DTO requirement.
