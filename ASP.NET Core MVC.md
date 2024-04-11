#asp-net
## Routing
- ASP.NET Core controllers use Routing [[middleware]] to match URLs of incoming requests and map them to actions
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