#asp-net
## Technologies
- [[Razor]]
- [[Entity Framework Core]]
- [[Dependency Injection (DI)]]
## Views
- handles data presentation and user interaction
- a view is an HTML template with [[Razor]] markup
- `cshtml` files
- View files are often grouped into folders named for each [controller](Controllers)
	- `Views/[ControllerName]`
	- `Views/Shared` - views shared among controllers
- Layouts
- Partial views
	- **reusable parts of views**
- View components
	- **similar to partial views**, but appropriate for view content that requires code to run on server
	- useful when rendered content requires database interaction (e.g. website shopping cart)

- Razor markup starts with `@`
```html
@{
    ViewData["Title"] = "About";
}
<h2>@ViewData["Title"].</h2>
<h3>@ViewData["Message"]</h3>

<p>Use this area to provide additional information.</p>
```
### Controllers and Views (View discovery)
- Views are usually returned from actions as a `ViewResult`, a type of `ActionResult`
	- you can return `ViewResult` directly, but that's not common -> just use `return View();`
`HomeController.cs`
```c#
public IActionResult About()
{
    ViewData["Message"] = "Your application description page.";

    return View();
}
```
- When an action returns a view, *view discovery* process takes place -> determines which view file is used based on view name (e.g `About ActionResult` searches `About.cshtml` in `Views/[ControllerName]/[ViewName]` or `Shared/[ViewName]`)
	- or can be provided instead
		- `return View("Views/Home/About.cshtml");`
		- `return View("../Manage/Index");` - relative
		- `return View("./About");` - controller-specific
### Pass data to views
#### Strongly-typed data (viewmodel)
- Pass an instance of viewmodel to the view from the action
- Viewmodels (different from business models) are typically either stored in `Models` or `ViewModels` folder
```html
@model WebApplication1.ViewModels.Address

<h2>Contact</h2>
<address>
    @Model.Street<br>
    @Model.City, @Model.State @Model.PostalCode<br>
    <abbr title="Phone">P:</abbr> 425.555.0100
</address>
```

```c#
public IActionResult Contact()
{
    ViewData["Message"] = "Your contact page.";

    var viewModel = new Address()
    {
        Name = "Microsoft",
        Street = "One Microsoft Way",
        City = "Redmond",
        State = "WA",
        PostalCode = "98052-6399"
    };

    return View(viewModel);
}
```
#### Weakly typed data (ViewData, \[ViewData\], ViewBag)
- ViewData is a dictionary of weakly typed objects
- ViewBag is a wrapper around ViewData that provides dynamic properties for ViewData collection
	- Key lookups are case-insensitive for both
##### ViewData
- `ViewDataDictionary` object
- access through string keys -> `ViewBag["string"]`
```c#
public IActionResult SomeAction()
{
    ViewData["Greeting"] = "Hello";
    ViewData["Address"]  = new Address()
    {
        Name = "Steve",
        Street = "123 Main St",
        City = "Hudson",
        State = "OH",
        PostalCode = "44236"
    };

    return View();
}
```

```html
@{
    // Since Address isn't a string, it requires a cast.
    var address = ViewData["Address"] as Address;
}

@ViewData["Greeting"] World!

<address>
    @address.Name<br>
    @address.Street<br>
    @address.City, @address.State @address.PostalCode
</address>
```
##### \[ViewData\]
- `ViewDataAttribute`
- properties on controllers/Razor page models with `[ViewData]` have their values stored and loaded from dictionary
```c#
public class HomeController : Controller
{
    [ViewData]
    public string Title { get; set; }

    public IActionResult About()
    {
        Title = "About Us";
        ViewData["Message"] = "Your application description page.";

        return View();
    }
}
```

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```
##### ViewBag
*Not available by default in Razor Pages `PageModel` classes*
- `Microsoft.AspNetCore.Mvc.ViewFeatures.Internal.DynamicViewData`
- provides dynamic access to objects in `ViewData` -> does not require casting
	- basically accesses the same data as `ViewData`
```c#
public IActionResult SomeAction()
{
    ViewBag.Greeting = "Hello";
    ViewBag.Address  = new Address()
    {
        Name = "Steve",
        Street = "123 Main St",
        City = "Hudson",
        State = "OH",
        PostalCode = "44236"
    };

    return View();
}
```

```html
@ViewBag.Greeting World!

<address>
    @ViewBag.Address.Name<br>
    @ViewBag.Address.Street<br>
    @ViewBag.Address.City, @ViewBag.Address.State @ViewBag.Address.PostalCode
</address>
```
##### Dynamic views
When `@model` is not declared, but have a model instance passed (e.g. `return View(Address);`) 
```html
<address>
    @Model.Street<br>
    @Model.City, @Model.State @Model.PostalCode<br>
    <abbr title="Phone">P:</abbr> 425.555.0100
</address>
```
##### [CSS Isolation](https://learn.microsoft.com/en-us/aspnet/core/mvc/views/overview?view=aspnetcore-8.0#css-isolation)
## Routing
- ASP.NET Core controllers use Routing [middleware](Middleware.md) to match URLs of incoming requests and map them to actions
	- Routing middleware: `UseRouting` and `UseEndpoints`
		- Apps don't need to call these since `WebApplicationBuilder` wraps middleware added to `Program.cs` with `UseRouting` and `UseEndpoints`
	- Call `MapControllers` to map attribute-routed controllers
	- Call `MapControllerRoute` or `MapAreaControllerRoute` to map both conventionally-routed controllers and attribute-routed controllers
- Route templates
	- are defined at startup in `Program.cs`
	- describe how URL paths are matched to actions
	- generate URLs for links in responses
- Actions either conventionally-routed, or attribute-routed
	- **attribute-routed**: Explicitly defining route on a controller or action
		- REST APIs use attribute-routing
	- **conventionally-routed**: uses `MapControllerRoute` to create a single route
### Syntax
Code below uses *route templating* for the `pattern` parameter. `name` is the name of the route.

`Program.cs`
```c#
...
app.MapControllerRoute(
	name: "default", 
	pattern: "{controller=Home}/{action=Index}/{id?}");
...
```
- Convenience method: `app.MapDefaultControllerRoute();`

Code below matches URL `/Products/Details/5`.

`ProductsController.cs`
```c#
public class ProductsController : Controller
{
    public IActionResult Details(int id)
    {
        return ControllerContext.MyDisplayRouteInfo(id);
    }
}
```
#### Multiple conventional routes
```c#
app.MapControllerRoute(name: "blog",
                pattern: "blog/{*article}",
                defaults: new { controller = "Blog", action = "Article" });
app.MapControllerRoute(name: "default",
               pattern: "{controller=Home}/{action=Index}/{id?}");
```
#### Attribute routing/HTTP verbs
```c#
[Route("api/[controller]")]
[ApiController]
public class Test2Controller : ControllerBase
{
    [HttpGet]   // GET /api/test2
    public IActionResult ListProducts()
    {
        return ControllerContext.MyDisplayRouteInfo();
    }

    [HttpGet("{id}")]   // GET /api/test2/xyz
    public IActionResult GetProduct(string id)
    {
       return ControllerContext.MyDisplayRouteInfo(id);
    }

    [HttpGet("int/{id:int}")] // GET /api/test2/int/3
    public IActionResult GetIntProduct(int id)
    {
        return ControllerContext.MyDisplayRouteInfo(id);
    }

    [HttpGet("int2/{id}")]  // GET /api/test2/int2/3
    public IActionResult GetInt2Product(int id)
    {
        return ControllerContext.MyDisplayRouteInfo(id);
    }
}
```
## References
https://learn.microsoft.com/en-us/aspnet/core/mvc/controllers/routing?view=aspnetcore-8.0