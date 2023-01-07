# log

## 2022-07-01

[Asynchronous programming patterns](https://learn.microsoft.com/en-us/dotnet/standard/asynchronous-programming-patterns/)

three patterns:

1. Event-based asynchronous pattern (EAP)
2. Asynchronous programming model (APM)
3. Task-based asynchronous pattern (TAP)

### TAP 

TAP is the recommended pattern for asynchronous programming in .NET. It uses the Task and Task<T> types and the async and await keywords to represent asynchronous operations. The async and await keywords provide a convenient syntax for writing asynchronous code without manually manipulating threads. The Task and Task<T> types are abstractions that represent operations running in the background.