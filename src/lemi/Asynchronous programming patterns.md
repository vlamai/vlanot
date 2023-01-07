# Asynchronous programming patterns

[Asynchronous programming patterns](https://learn.microsoft.com/en-us/dotnet/standard/asynchronous-programming-patterns/)

three patterns:

1. Event-based asynchronous pattern (EAP)
2. Asynchronous programming model (APM)
3. Task-based asynchronous pattern (TAP)

## TAP

witch use `Task` and `Task<T>` to represent asynchronous operations and  uses a single method to represent the initiation and completion of an asynchronous operation.

[Consuming the Task-based Asynchronous Pattern](https://learn.microsoft.com/en-us/dotnet/standard/asynchronous-programming-patterns/consuming-the-task-based-asynchronous-pattern)

[Implementing the Task-based Asynchronous Pattern](https://learn.microsoft.com/en-us/dotnet/standard/asynchronous-programming-patterns/implementing-the-task-based-asynchronous-pattern)

### Initiate an asynchronous operation

Async method based on the TAP can do a small amount of work and then return a task that represents the work that is still to be done.

```csharp
public Task<int> GetLengthAsync(string name)
{
    // Do a small amount of work.
    if (name == null)
    {
        throw new ArgumentNullException("name");
    }

    // Return a task that represents the work that is still to be done.
    return Task.Run(() => name.Length);
}
```

Reasons:

1. The task returned by the method can be awaited by the caller.
2. may be called from UI thread, so it can't block the UI thread.

### Exceptions

If an exception is thrown in the asynchronous operation, the task returned by the method will be faulted.

```csharp
public Task<int> GetLengthAsync(string name)
{
    // Do a small amount of work.
    if (name == null)
    {
        throw new ArgumentNullException("name");
    }

    // Return a task that represents the work that is still to be done.
    return Task.Run(() =>
    {
        // Simulate a failure.
        if (name == "error")
        {
            throw new ArgumentException("error");
        }

        return name.Length;
    });
}
```
