#asp-net
- app pipeline that handles HTTP requests and responses[^ms]
	- components (*request delegates*) can choose to pass request to next component and perform work before/after next component in pipeline
	- request delegates handle each HTTP request
- `Run()`, `Map()`, `Use()` extension methods
	- Run delegates are terminal (short-circuit the request pipeline)
## Diagrams and Syntax
![](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/middleware/index/_static/request-delegate-pipeline.png?view=aspnetcore-8.0)

![](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/middleware/index/_static/middleware-pipeline.svg?view=aspnetcore-8.0)

```c#
var builder = WebApplication.CreateBuilder(args); 
var app = builder.Build(); 

app.Run(async context => { await context.Response.WriteAsync("Hello world!"); }); 

app.Run();
```
- Simplest possible ASP.NET Core app
## References
[^ms]: https://learn.microsoft.com/en-us/aspnet/core/fundamentals/middleware/?view=aspnetcore-8.0
[[Difference between Services and Middleware]]