- Object Relational Mapper (O/RM)
	- generates tables and table migrations/patches 
- Data access is performed using a model
	- a model is made up of 
		- **entity classes** (tables) and 
		- a **context object** (`ApplicationDbContext`) that represents a session with the database
			- allows querying and saving data
- Instances of entity classes are retrieved using [LINQ](obsidian://open?vault=ASP-Notes&file=Language%20Integrated%20Query).
```c#
using (var db = new BloggingContext())
{
    var blogs = db.Blogs
        .Where(b => b.Rating > 3)
        .OrderBy(b => b.Url)
        .ToList();
}
```
- Data is created, deleted, and modified using instances of entity classes
```c#
using (var db = new BloggingContext())
{
    var blog = new Blog { Url = "http://sample.com" };
    db.Blogs.Add(blog);
    db.SaveChanges();
}
```

### Sample Model (context class and entity class)
```c#
using Microsoft.EntityFrameworkCore;
using System;
using System.Collections.Generic;

public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }

    public string DbPath { get; }

    public BloggingContext()
    {
        var folder = Environment.SpecialFolder.LocalApplicationData;
        var path = Environment.GetFolderPath(folder);
        DbPath = System.IO.Path.Join(path, "blogging.db");
    }

    // The following configures EF to create a Sqlite database file in the
    // special "local" folder for your platform.
    protected override void OnConfiguring(DbContextOptionsBuilder options)
        => options.UseSqlite($"Data Source={DbPath}");
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }

    public List<Post> Posts { get; } = new();
}

public class Post
{
    public int PostId { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public int BlogId { get; set; }
    public Blog Blog { get; set; }
}
```
## Steps[^tutorialsEU]
Note that this tutorial does not use MVC- but a plain C# implementation demonstrating Entity Framework
1. Create class for table
	 - EF creates entities
```c#
public class Purchase 
{
	public int Id { get; set; }
	public string? Product { get; set; }
	public decimal Price { get; set; }
}
```
2. Create `ApplicationDbContext` to enable EF to create entities from model 
```c#
	public class MyDbContext : DbContext
	{
		public DbSet<Purchase> Purchases { get; set; }
		
	}
```
3. Configure `ApplicationDbContext` to use a running database
```c#
public class MyDbContext : DbContext...
//For configuring the server; typically done as a service in Program.cs
		protected override void OnConfiguring(DbContextOptionsBuildr optionsBuilder) 
		{
			//optionsBuilder.UseSqlServer(...);
			optionsBuilder.UseInMemoryDatabase("MyDb");
		}
...
```
4. Use the database
```c#
static void Main(string[] args)
{
	//Typically created as a service in Program.cs
	using (var context = new MyDbContext())
	{
		var purchase = new Purchase()
		{
			Id = 1,
			Product = "Shoes",
			Price = 49.95m
		};

		//Creating 
		context.Purchases.Add(purchase);

		//Deleting
		context.Purchases.Remove(purchase);

		//Updating
		context.Purchases.Update(purchase);

		//FirstOrDefault - give first item matching condition
		var purchaseFirst = context.Purchases.FirstOrDefault(p => p.Product == "Shoes" );

		//Where
		var purchaseWhere = context.Purchases.Where(p => p.Price < 100m);

		//Get entire table
		var allPurchases = context.Purchases.ToList();
		
		//Saving and Querying Database
		context.SaveChanges();	
	}
}
```


- [[Navigation property]]

## References
[^tutorialsEU]: https://www.youtube.com/watch?v=gQ2yBG3-jKI
### Related
[[Entity Framework 6]]