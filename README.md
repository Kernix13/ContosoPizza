# Full CRUD RESTful Service with ASP.NET Core and Controllers in C#

A RESTful service for pizza inventory management for a pizza company's web storefront and mobile application.

<!--
  namespace: ContosoPizza
  GitHub slug: ContosoPizza
  About text: A C# .NET 8.0 API app with Controllers, Models, and Services.
 -->

<span aria-hidden="true"><br></span>

## Prerequisites

- [.NET SDK](https://dotnet.microsoft.com/en-us/download) 8.0
- [Visual Studio Code](https://code.visualstudio.com/) with [C# Dev Kit](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csdevkit)

<span aria-hidden="true"><br></span>

## Installation & Usage

1. Clone this repository and switch into project folder

   ```sh
   git clone https://github.com/Kernix13/ContosoPizza.git
   cd ContosoPizza
   ```

2. Run the application

   ```bash
   dotnet run
   ```

3. Build the application

   ```bash
   dotnet build
   ```

### <span aria-hidden="true">⚡</span> Quick Start

```sh
git clone https://github.com/Kernix13/ContosoPizza.git
cd ContosoPizza
dotnet run
```

<span aria-hidden="true"><br></span>

## Syntax and notes for this project

> Why are classes either in `Models/` or `Services/`? Which is it?

Steps: Models then Services then Controllers

### <span aria-hidden="true">📌</span> Create a web API with ASP.NET Core controllers

<!-- https://learn.microsoft.com/en-us/training/modules/build-web-api-aspnet-core/ -->

- `GET`: Retrieve data from the web service.
- `POST`: Create a new item of data on the web service.
- `PUT`: Update an item of data on the web service.
- `DELETE`: Delete an item of data on the web service.
- `PATCH`: Update an item of data on the web service by describing a set of instructions about how the item should be modified. The sample application in this module doesn't use this verb.

#### Benefits of creating APIs in ASP.NET Core:

- You can use the same framework and patterns to build both webpages and services
- Endpoints automatically serialize your classes to properly formatted JSON out of the box
- API endpoints have built-in support for industry-standard JSON Web Tokens (JWTs).
- ASP.NET lets you define routes and verbs inline with your code by using attributes. Data from the request path, query string, and request body are automatically bound to method parameters
- HTTPS by default: end-to-end encryption to provide privacy and to help ensure that your API calls aren't intercepted and altered between client and server

```sh
dotnet new webapi -controllers -f net8.0
dotnet run
```

- This command creates the files for a basic web API project that uses controllers
- `Controllers/`: Contains classes with public methods exposed as HTTP endpoints.
- By convention, controller class names are suffixed with `Controller`
- The actions are exposed as HTTP endpoints inside the web API controller
- A controller is a public class with one or more public methods known as actions
  - The actions are exposed as HTTP endpoints via routing
  - an HTTP GET request to `https://localhost:{PORT}/weatherforecast` causes the `Get()` method of the `WeatherForecastController` class to be executed
  - inherits from the `ControllerBase` base class, the base class for working with HTTP requests in ASP.NET Core
- includes the two standard attributes: `[ApiController]` and `[Route]`:
  - the `[Route]` attribute defines a mapping to the `[controller]` token
  - this controller handles requests to `https://localhost:{PORT}/api/pizza`
  - The full endpoint is `http://localhost:5118/api/pizza`
- A port from 5000 to 5300 is selected for HTTP, and from 7000 to 7300 for HTTPS, when the project is created
- `Program.cs`: Configures services and the app's HTTP request pipeline, and contains the app's managed entry point.
- `.csproj`: Contains configuration metadata for the project

#### API controller class attributes:

```cs
[ApiController]
[Route("api/[controller]")]
// [Route("[controller]")]
public class PizzaController : ControllerBase {}
```

- `[ApiController]` enables opinionated behaviors that make it easier to build web APIs. Some behaviors include:
  - parameter source inference,
  - attribute routing as a requirement, and
  - model validation error-handlingenhancements\*.
- `[Route]` defines the routing pattern `[controller]`.
  - The controller's name (case-insensitive, without the Controller suffix) replaces the `[controller]` token
  - handles requests to `https://localhost:{PORT}/weatherforecast`
- `[HttpGet(Name = "GetWeatherForecast")]`: routes HTTP GET requests to the `public IEnumerable<WeatherForecast> Get()` method
- In ASP.NET, you can retrieve a single item by using the `[HttpGet("{id}")]` attribute

```cs
[ApiController]
[Route("[controller]")]
public class PizzaController : ControllerBase
{
    public PizzaController()
    {
    }

    // GET all action

    // GET by Id action

    // POST action

    // PUT action

    // DELETE action
}
```

#### Http file:

- `.http`: Contains configuration to test REST APIs directly from VS Code
  - select the _Sent Request_ command above the GET which sends a request to the running service
- Add a new GET to call the Pizza endpoint under the `### seperator`
- Select the Send Request command above this new GET call
- To query for a single pizza, you can make another GET request, but pass in an id parameter by using the following command:

```http
@ContosoPizza_HostAddress = http://localhost:5118

GET {{ContosoPizza_HostAddress}}/api/pizza/1
Accept: application/json

###
```

> [!NOTE]
> The _Send Request_ links in my `.http` file are not working. I saw an error about port 5117 being used so I changed it to 5118 and that opened the Swagger UI in the browser but the Send Request links still do not work. I get the same error when I run `dotnet run` and try a request from the .http file.

```
warn: Microsoft.AspNetCore.HttpsPolicy.HttpsRedirectionMiddleware[3]
      Failed to determine the https port for redirect.
```

So how do you test? Do you use Swagger or do you use the .http file?

#### Data store and models

You need a model class to represent an object in your inventory. The model contains properties that represent the characteristics of the object. The model is used to pass data in the web API and to persist object options in the data store.

- The directory name Models is a convention
- You need a model class to represent a pizza in your inventory
- A model is a set of classes that represent the data that the app manages
- The model contains properties that represent the characteristics of a pizza
- The model is used to pass data in the web API and to persist pizza options in the data store
- The data store is a simple local in-memory caching service. In a real-world application, you'd consider using a database
- Pizza.cs is a public class
- Model classes can go anywhere in the project, but the `Models` folder is used by convention

```cs
// namespace ProjectName.FolderName;
namespace ContosoPizza.Models;

public class Pizza
{
    public int Id { get; set; }
    public string? Name { get; set; }
    public bool IsGlutenFree { get; set; }
}
```

#### Services

See `Services/PizzaService.cs`

A Service is a class that holds your business logic

- PizzaService.cs provides a simple in-memory data caching service (for demo purposes)
  - The constructor creates 2 pizza objects
  - method to retreive all pizza objects
  - method to find a pizza by id
  - method to add a new pizza object
  - method to delete a pizza object by id
  - method to update a pizza object by id

### <span aria-hidden="true">📌</span> CRUD actions in ASP.NET Core

- GET: Read -> [HttpGet]
- POST: Create -> [HttpPost]
- PUT: Update -> [HttpPut]
- DELETE: Delete -> [HttpDelete]

#### GET

- GET: use the built-in `[HttpGet]` attribute to define a method that returns the pizzas from our service
- The `ActionResult` type is the base class for all action results in ASP.NET Core.
- It automatically returns data with a `Content-Type` value of `application/json`

```cs
// controllers/PizzaController.cs
[HttpGet]
public ActionResult<List<Pizza>> GetAll() =>
    PizzaService.GetAll();
```

- Use the built-in `[HttpGet("{id}")]` attribute to define a method that returns the pizzas from our service
- The routing logic registers `[HttpGet]` (without id) and `[HttpGet("{id}")]` (with id) as two different routes
- Remember, the controller-level `[Route]` attribute defined the `/pizza` pattern (?)
- ASP.NET Core action result - `Ok` -> `200`
- ASP.NET Core action result - `NotFound` -> `404`

#### POST

To enable users to add a new item to the endpoint, you must implement the POST action by using the `[HttpPost]` attribute

- ASP.NET Core automatically converts any application/JSON sent to the endpoint into a populated .NET `Pizza` object:

```cs
[HttpPost]
public IActionResult Create(Pizza pizza)
{
    PizzaService.Add(pizza);
    return CreatedAtAction(nameof(Get), new { id = pizza.Id }, pizza);
}
```

- Inserts the request body's `Pizza` object into the in-memory cache
- The `[HttpPost]` attribute maps HTTP POST requests sent to `http://localhost:5000/api/pizza` by using the `Create()` method
- This method returns an `IActionResult` response
- `IActionResult` lets the client know if the request succeeded and provides the ID of the newly created pizza (how/where?)
- `IActionResult` uses standard HTTP status codes, so it can easily integrate with clients regardless of the language or platform they're running on
- `CreatedAtAction`: 201 - The pizza is included in the response body in the media type, as defined in the `accept` HTTP request header (JSON by default).
- `BadRequest` is implied: 400 - The request body's pizza object is invalid
- `ControllerBase` has utility methods that create the appropriate HTTP response codes and messages for you
- **NOTE**: Because the controller is annotated with the `[ApiController]` attribute, it's implied that the `Pizza` parameter will be found in the request body.
- The first parameter in the `CreatedAtAction` method call represents an action name.
  - The `nameof` keyword is used to avoid hard-coding the action name.
- `CreatedAtAction` uses the action name to generate a `location` HTTP response header with a URL to the newly created pizza.

#### PUT

Modifying or updating a pizza in our inventory is similar to the POST method that you implemented, but it uses the `[HttpPut]` attribute and takes in the `id` parameter in addition to the `Pizza` object that needs to be updated

```cs
[HttpPut("{id}")]
public IActionResult Update(int id, Pizza pizza)
{
    if (id != pizza.Id)
        return BadRequest();

    var existingPizza = PizzaService.Get(id);
    if(existingPizza is null)
        return NotFound();

    PizzaService.Update(pizza);

    return NoContent();
}
```

Each `ActionResult` instance used in the preceding action is mapped to the corresponding HTTP status code in the following table:

- `NoContent`: 204 - pizza was updated in the in-memory cache
- `BadRequest`: 400 - The request body's `Id` value doesn't match the route's `id` value. The request body's `Pizza` object is invalid
- Returns `IActionResult`, because the `ActionResult` return type isn't known until runtime.
- The `BadRequest`, `NotFound`, and `NoContent` methods return `BadRequestResult`, `NotFoundResult`, and `NoContentResult` types, respectively
- **NOTE**: Because the controller is annotated with the `[ApiController]` attribute, it's implied that the `Pizza` parameter will be found in the request body.

#### DELETE

One of the easier actions to implement is the `DELETE` action, which takes in just the `id` parameter of the pizza to remove from the in-memory cache:

```cs
[HttpDelete("{id}")]
public IActionResult Delete(int id)
{
    var pizza = PizzaService.Get(id);

    if (pizza is null)
        return NotFound();

    PizzaService.Delete(id);

    return NoContent();
}
```

Each `ActionResult` instance used in the preceding action is mapped to the corresponding HTTP status code in the following table:

- `NoContent`: 204 - The pizza was deleted from the in-memory cache.
- `NotFound`: 404 - A pizza that matches the provided `id` parameter doesn't exist in the in-memory cache.
- Returns `IActionResult` because the `ActionResult` return type isn't known until runtime. The `NotFound` and `NoContent` methods return `NotFoundResult` and `NoContentResult` types, respectively.
- Queries the in-memory cache for a pizza that matches the provided `id` parameter.

<span aria-hidden="true"><br></span>

## Interact with an ASP.NET Core minimal API

> This section was not required for the project but has related topics

- Controller-based API: approach to building APIs in which each endpoint is mapped to a specific controller class
  - The controller handles the request, performs any necessary business logic, and returns a response
  - Consists of one or more controller classes that derive from `ControllerBase`

```cs
[ApiController]
[Route("[controller]")]
public class WeatherForecastController : ControllerBase
```

- `Controller` derives from `ControllerBase` and adds support for views, so it's for handling web pages, not web API requests.
- The `ControllerBase` class provides many properties and methods that are useful for handling HTTP requests

Minimal APIs:

- A simplified approach for building fast HTTP APIs with ASP.NET Core
- Build fully functioning REST endpoints with minimal code & configuration
- They support the configuration and customization needed to:
  - scale to multiple APIs,
  - handle complex routes,
  - apply authorization rules, and
  - control the content of API responses

```cs
var app = WebApplication.Create(args);
app.MapGet("/", () => "Hello World!");
app.Run();
```

<span aria-hidden="true"><br></span>

## Methods in Program.cs

```cs
// Initialize the builder with command-line arguments:
var builder = WebApplication.CreateBuilder(args);

/* DEPENDENCY INJECTION */
// Add services: Registers API controllers:
builder.Services.AddControllers();
// Generates the data structure:
builder.Services.AddEndpointsApiExplorer();
// Creates the webpage UI:
builder.Services.AddSwaggerGen();

// Build the application instance:
var app = builder.Build();

/* MIDDLEWARE PIPELINE */
// Configure HTTP request path
if (app.Environment.IsDevelopment())
{
    app.UseSwagger();
    app.UseSwaggerUI();
}

// Enable HTTPS redirection middleware
app.UseHttpsRedirection();

// Enable authorization in an ASP.NET Core application
app.UseAuthorization();

// Map Endpoints
app.MapControllers();

// Start the web application
app.Run();
```

<!--

This requires a specific .NET 9 package dependency (Microsoft.AspNetCore.OpenApi):
app.MapOpenApi();

 -->
