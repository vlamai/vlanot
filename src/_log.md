# log

## 2021-01-01

How to find in C# code all class that implement interface?

```csharp

var types = AppDomain.CurrentDomain.GetAssemblies()
    .SelectMany(s => s.GetTypes())
    .Where(p => typeof(IInterface).IsAssignableFrom(p));
```

How to find in c# all class that implement interface with specific generic type?

```csharp

var types = AppDomain.CurrentDomain.GetAssemblies()
    .SelectMany(s => s.GetTypes())
    .Where(p => typeof(IInterface<>).IsAssignableFrom(p) && p.IsGenericType);
```

## 2023-14-01

[Снижение аллокации при замыкании (closure)](https://habr.com/ru/post/677818/)
[Know Thine Implicit Allocations](https://devblogs.microsoft.com/pfxteam/know-thine-implicit-allocations/)
[Замыкания в языке программирования C#](http://sergeyteplyakov.blogspot.com/2010/04/c.html)

## 2022-07-01

[For Further Reading (Parallel Programming)](https://learn.microsoft.com/en-us/dotnet/standard/parallel-programming/for-further-reading-parallel-programming)
