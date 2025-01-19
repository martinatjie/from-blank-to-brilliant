# Welcome to the **Galactic Pet Adoption Agency!** 🪐🐾

Ever wanted to manage **space pets** from across the galaxy? Now you can! This example API is a guide to mastering C# Web API controllers, with a focus on most common patterns and syntax rules. And a cosmic twist, of course. 🚀

Here’s what you’ll learn as you help adorable *Moon Rabbits* and *Martian Floofers* find their forever homes:

🌟 How to set up controllers (no rocket science required!)
🌟 The magic of HTTP method **attributes** and **routing patterns**
🌟 **Parameter binding** made easy, with crystal-clear examples
🌟 How to use **return types and status codes** like a dev at NASA
🌟 **Route templates** de-spacified
🌟 Best practices to keep your API clean, structured, well-named and ready for launch so that even aliens on Neptune can use it

It’s not just coding—it’s an interstellar adventure in learning! Let’s build APIs that are out of this world! ✨

---

# C# Web API Controller Syntax Guide

## Controller Setup

```csharp
// Basic controller
[ApiController]  // Enables automatic model validation
[Route("api/[controller]")]  // Base route using controller name
public class PetsController : ControllerBase
{
    // Controller methods go here
}

// Custom base route
[Route("api/v1/accessories")]  // Fixed route
public class AccessoriesController : ControllerBase
{
}
```

## HTTP Method Attributes & Route Patterns

### GET Requests
```csharp
// GET api/pets
[HttpGet]
public IActionResult GetAll() { }

// GET api/pets/5
[HttpGet("{id}")]
public IActionResult GetById(int id) { }

// GET api/pets/5/details
[HttpGet("{id}/details")]
public IActionResult GetDetails(int id) { }

// GET api/pets?species=axolottis
[HttpGet]
public IActionResult GetBySpecies([FromQuery] string species) { }

// GET api/pets/by-date/2024-01-18
[HttpGet("by-date/{date}")]
public IActionResult GetByDate(DateTime date) { }
```

### POST Requests
```csharp
// POST api/pets
[HttpPost]
public IActionResult Create([FromBody] Pet pet) { }

// POST api/pets/bulk
[HttpPost("bulk")]
public IActionResult CreateMany([FromBody] IEnumerable<Pet> pets) { }

// POST with form data
[HttpPost("upload")]
public IActionResult Upload([FromForm] IFormFile file) { }
```

### PUT Requests
```csharp
// PUT api/pets/5
[HttpPut("{id}")]
public IActionResult Update(int id, [FromBody] Pet pet) { }

// PUT with multiple parameters
[HttpPut("{id}/status")]
public IActionResult UpdateStatus(int id, [FromBody] StatusUpdate status) { }
```

### DELETE Requests
```csharp
// DELETE api/pets/5
[HttpDelete("{id}")]
public IActionResult Delete(int id) { }

// DELETE with query parameters
[HttpDelete]
public IActionResult DeleteMany([FromQuery] string[] ids) { }
```

## Parameter Binding Attributes

### When to Use Each Parameter Attribute
```csharp
// [FromBody] - For JSON request body
public IActionResult Create([FromBody] Pet pet) { }

// [FromQuery] - For query string parameters
public IActionResult Search([FromQuery] string term) { }

// [FromRoute] - For route parameters (optional, as route params bind automatically)
public IActionResult Get([FromRoute] int id) { }

// [FromForm] - For form data and file uploads
public IActionResult Upload([FromForm] IFormFile file) { }

// [FromHeader] - For header values
public IActionResult Process([FromHeader(Name = "X-Custom-Header")] string header) { }
```

## Return Types & Status Codes

### Common Return Patterns
```csharp
// Return specific types
[HttpGet]
public ActionResult<Pet> Get(int id)
{
    var pet = // ... get pet
    if (pet == null)
        return NotFound();
    return pet;
}

// Return IActionResult for more control
[HttpPost]
public IActionResult Create([FromBody] Pet pet)
{
    // ... create pet
    return CreatedAtAction(nameof(Get), new { id = pet.Id }, pet);
}

// Return type with status code
[HttpGet]
public ActionResult<IEnumerable<Pet>> GetAll()
{
    var pets = // ... get pets
    return Ok(pets);
}
```

## Route Templates Guide

### Basic Rules:
- Use kebab-case for multi-word route segments: `pet-species` instead of `petSpecies`
- Route parameters in curly braces: `{id}`
- Optional parameters with question mark: `{id?}`
- Multiple parameters separated by slash: `{species}/{id}`

### Examples:
```csharp
// Base controller route options
[Route("api/[controller]")]        // Uses controller name
[Route("api/pets")]            // Fixed route
[Route("api/v{version:apiVersion}/[controller]")] // Versioned

// Method route combinations
[HttpGet]                         // Uses base route
[HttpGet("active")]              // Appends to base route
[HttpGet("/api/pets")]       // Absolute route (ignores base)
[HttpGet("{id:int}")]           // With type constraint
[HttpGet("{id:guid?}")]         // Optional parameter
```

## Best Practices

### Naming Conventions:
1. Controller names should be plural nouns suffixed with "Controller"
   - `PetsController`, `AdoptersController`

2. Action method naming:
   - GET: Start with "Get" - `GetAll()`, `GetById()`
   - POST: Use "Create" or "Add" - `Create()`, `AddAccessory()`
   - PUT: Use "Update" or "Replace" - `Update()`, `ReplaceAccessory()`
   - DELETE: Use "Delete" or "Remove" - `Delete()`, `RemoveAccessory()`

### Route Structure:
1. Resources:
   - Collection: `api/pets`
   - Single accessory: `api/pets/{id}`
   - Sub-resource: `api/pets/{id}/stories`

2. Actions:
   - Standard CRUD: Use HTTP methods
   - Custom actions: Use descriptive names
     - `api/pets/{id}/archive`
     - `api/adoptions/{id}/cancel`


## Combining Controller and Method Routes

### Method-Level [Route] Usage
```csharp
[ApiController]
[Route("api/pets")]  // Controller-level route
public class PetsController : ControllerBase
{
    [Route("")]  // Matches exactly "api/pets"
    [HttpGet]
    public IActionResult GetAll() { }

    [Route("{id}")]  // Matches "api/pets/{id}"
    [HttpGet]
    public IActionResult GetById(int id) { }

    [Route("featured")]  // Matches "api/pets/featured"
    [HttpGet]
    public IActionResult GetFeatured() { }

    // Combining [HttpGet] and [Route] into one attribute
    [HttpGet("endangered")]  // Matches "api/pets/endangered"
    public IActionResult GetEndangered() { }
}
```

### When to Use Method-Level [Route]
1. **Complex Routes**: When you need more control over the exact route structure
   ```csharp
   [Route("by-species/{species}/sort-by/{sortField}")]
   [HttpGet]
   public IActionResult GetSorted(string species, string sortField) { }
   ```

2. **Multiple Routes for One Action**: When an action needs to handle multiple URL patterns
   ```csharp
   [Route("search")]
   [Route("find")]  // This action handles both "api/pets/search" and "api/pets/find"
   [HttpGet]
   public IActionResult Search([FromQuery] string term) { }
   ```

3. **Absolute Routes**: When you need to override the controller-level route completely
   ```csharp
   [Route("~/api/v2/adopted-pets")]  // The ~/ prefix ignores the controller-level route
   [HttpGet]
   public IActionResult GetAdoptedPets() { }
   ```

### Common Patterns

```csharp
[ApiController]
[Route("api/[controller]")]
public class AdoptionsController : ControllerBase
{
    // Pattern 1: Using HTTP attribute with route parameter
    [HttpGet("{id}")]  // Preferred when route is simple
    public IActionResult GetAdoption(int id) { }

    // Pattern 2: Separate Route and HTTP attributes
    [Route("by-adopter/{adopterId}")]
    [HttpGet]
    public IActionResult GetAdopterAdoptions(int adopterId) { }

    // Pattern 3: Multiple route patterns
    [Route("recent")]
    [Route("latest")]
    [HttpGet]
    public IActionResult GetRecent() { }

    // Pattern 4: Complex route with constraints
    [Route("{year:int}/{month:int}")]
    [HttpGet]
    public IActionResult GetByMonth(int year, int month) { }
}
```

### Best Practices for Route Attributes

1. **Choose the Right Approach**:
   - Use `[HttpGet("path")]` for simple routes (shorter syntax)
   - Use separate `[Route]` + `[HttpGet]` for complex routes or when multiple routes are needed

2. **Route Combining Rules**:
   ```csharp
   [Route("api/adoptions")]  // Controller route
   public class AdoptionsController : ControllerBase
   {
       [Route("active")]          // Results in "api/adoptions/active"
       [Route("current")]         // Also matches "api/adoptions/current"
       [HttpGet]
       public IActionResult GetActive() { }

       [Route("~/api/adoptions")]    // Ignores controller route, matches exactly "api/adoptions"
       [HttpGet]
       public IActionResult GetAll() { }
   }
   ```
