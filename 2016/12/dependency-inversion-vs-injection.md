# Dependency Inversion Principle and Dependency Injection

## Inversion

Dependency Inversion Principle states that higher level modules should define an abstraction upon which they declare their lower level needs.
A lower level module will have to conform to the abstraction defined by the higher level module.
This architecture choice put the emphasis on the higher level needs.
The consumer of a higher level module will have to conform to its needs.

This pattern is good when re-using higher level modules is important.
It is not so good when lower level module are re-used more often in practice.

A typical example is a logger service.
A business service want to log some business related data.
The business service declares an abstraction for logging.
The actual logger service can be implemented with a dependency to the business abstraction,
but that would limit its usage to that particular business service.
To solve this problem, we use the Adaptor design pattern.
In the final application package, an adaptor will implement the business abstraction only to delegate to the actual logger service.

Repeating this process for every application in a company would be tedious.
Hence, when a higher level business module needs to log, it can depend on the company’s logger abstraction.
The adaptor would need to be implemented once for every actual logger implementation and could be re-used across applications in the same company.

Here is a textual representation of all components and their dependencies:

```
LoggerPackage:Logger
BusinessPackage:BusinessService -> CompanyPackage:BusinessLogger
BusinessPackage:AnotherBusinessService -> CompanyPackage:BusinessLogger
CompanyAdaptorsPackage:BusinessLoggerAdaptor -> [CompanyPackage:BusinessLogger, LoggerPackage:Logger]
ApplicationPackage:Programm -> [LoggerPackage:Logger, CompanyAdaptorsPackage:BusinessLoggerAdaptor, BusinessPackage:BusinessService]
```

## Injection

On the other hand, Dependency Injection is simply the fact that components should not instantiate their dependencies by themselves.
They should accept an instance of the said dependency and simply use it.
A common practice is to *inject* the dependencies via the object’s constructor, hence the dependency injection terminology.

In other words, no re-usable component should use the `new` keyword to instantiate a dependency, whether it be an abstraction or a concretion.
This will be the role of the application to glue all the pieces together.

# Altogether

In the end, a customer application will depend on all its dependencies' dependencies.
This is where we wire up all the dependencies stuff:

- dependency resolution (inverse of inversion)
- dependency injection (by hand or with specialized libraries)

Here is a pseudo-C# code sample showing both concepts working together:

**Application/Program.cs**

``` csharp
using BusinessPackage.BusinessService;
using CompanyAdaptorsPackage.BusinessLoggerAdaptor;
using LoggerPackage.Logger;

namespace ApplicationPackage {
    public class Program {
        // The application instantiate its own dependencies.
        static void Main() {
            // Pass application specific arguments to logger.
            var logger = new Logger( "path/to/dirlog", format: "JSON" );

            // BusinessLoggerAdaptor implements CompanyPackage.BusinessLogger.
            // Thus it is a BusinessLogger and it can be used by BusinessService.
            var loggerAdaptor = new BusinessLoggerAdaptor(logger);

            var businessService = new BusinessService(loggerAdaptor);

            // Use the `businessService` instance for whatever you need to.
            while (true) {
                businessService.makeMoney();
            }
        }
    }
}
```

**CompanyAdaptorsPackage/BusinessLoggerAdaptor.cs**

``` csharp
using CompanyPackage.BusinessLogger;
using LoggerPackage.Logger;

namespace CompanyAdaptorsPackage {
    public class BusinessLoggerAdaptor : BusinessLogger {
        private Logger logger;

        // The logger instance is injected.
        public ApplicationLoggerAdaptor(Logger logger) {
            this.logger = logger;
        }

        public void BusinessLogger.Log(string message) {
            this.logger.LoggerLogMethodName(message);
        }
    }
}
```
