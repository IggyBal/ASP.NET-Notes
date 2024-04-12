- Also referred to as *services*
- Code that uses (depends on) another piece of code, but instead of using it directly, it's passed on instead (injected)[^ca]
- In ASP.NET Core's MVC, it's when you create an interface (the service) and a concrete class that implements the interface (the implementation of the service)
	- Interfaces make sense because it boils down to the essential logic (methods) with different implementations (concrete classes implementing the interface) that do essentially the same thing with different technologies 
## StackOverflow[^so]
 "*It's important that `DataProcessor` does not create or rely on any specific `Logger` implementation, but just on the interface. This allows you to inject any concrete implementation of `Logger` during runtime.*"[^so]
```cpp
class Logger
{
public:
    virtual void writeMessage(std::string&) = 0;
}

class AppLogger : public Logger
{
public:
    virtual void writeMessage(std::string &str) { /* ... */ }
}

class TestLogger : public Logger
{
public:
    virtual void writeMessage(std::string &str) { /* ... */ }
}

class DataProcessor
{
public:
    DataProcessor(Logger &logger)   // dependency is injected via constructor
    {
        // The DataProcessor does NOT know anything about AppLogger or TestLogger.
        // In particular, the DataProcessor does NOT create any loggers!!
        // This is the key point of dependency injection - the dependencies are
        // provided from the outside of the class.

        this->logger = logger;      // save it for later
    }

    void ProcessSomeData()
    {   
        std::string msg("Processing some data");
        logger.writeMessage(msg);   // use the injected dependency
        // ...
    }

private:
    Logger logger;
}

void someFunctionSomewhere()
{
    AppLogger logger;
    DataProcessor processor(logger);    // inject the dependency here
    processor.ProcessSomeData();
}
```
## ASP.NET Core Syntax[^ms]
#asp-net
```c#
public interface IMyDependency
{
	void WriteMessage(string message);
}

public class MyDependency : IMyDependency
{
	public void WriteMessage(string message)
	{
		Console.WriteLine($"MyDependency.WriteMessage Message: {message}");
	}
}
```
## References
[^ca]: https://www.youtube.com/watch?v=J1f5b4vcxCQ&t=268s
[^so]: https://softwareengineering.stackexchange.com/questions/365092/how-is-dependency-injection-different-from-simple-interface-usage
[^ms]: https://learn.microsoft.com/en-us/aspnet/core/fundamentals/dependency-injection?view=aspnetcore-8.0
[[Services vs Middleware vs Controllers]]