# Design patterns

## Creational Patterns

- deal with object creation mechanisms, trying to create objects in a manner suitable to the situation
- explicit contructor, implicit DI, reflection, object pooling, etc.
- wholesale (single statement), piecewise (step-by-step) object construction

### Builder

Step-by-step construction of complex objects. The pattern allows you to produce different types and representations of an object using the same construction code.

#### Recirsive Generics

```csharp
var person = Person.New
    .Called("John")
    .WorksAsA("Developer")
    .Build();
```

```csharp
public class Person
{
    public string Name;
    public string Position;

    public class Builder : PersonJobBuilder<Builder>
    {
    }

    public static Builder New => new Builder();
}

public abstract class PersonBuilder
{
    protected Person person = new Person();
    public Person Build() => person;
}

public class PersonInfoBuilder<SELF> 
: PersonBuilder where SELF : PersonInfoBuilder<SELF>
{
    public SELF Called(string name)
    {
        person.Name = name;
        return (SELF)this;
    }
}

public class PersonJobBuilder<SELF> 
: PersonInfoBuilder<PersonJobBuilder<SELF>> where SELF : PersonJobBuilder<SELF>
{
    public SELF WorksAsA(string position)
    {
        person.Position = position;
        return (SELF)this;
    }
}
``` 

#### Sterpwise Builder

```csharp
var car = new CarBuilder()
    .WithType(CarType.SUV)
    .WithSeats(5)
    .Build();
```

```csharp
public enum CarType
{
    CityCar, SportsCar, SUV
}

public class Car
{
    public CarType Type { get; set; }
    public int Seats { get; set; }
}

public interface ISpecifyCarType
{
    ISpecifySeats WithType(CarType type);
}

public interface ISpecifySeats
{
    IBuildCar WithSeats(int seats);
}

public interface IBuildCar
{
    Car Build();
}

public class CarBuilder
{
  private class Impl : ISpecifyCarType, ISpecifySeats, IBuildCar
  {
    private Car car = new();

    public ISpecifySeats WithType(CarType type)
    {
      car.Type = type;
      return this;
    }

    public IBuildCar WithSeats(int seats)
    {
      switch (car.Type)
      {
        case CarType.CityCar:
          if (seats < 4)
            throw new ArgumentException("City cars must have at least 4 seats");
          break;
        case CarType.SportsCar:
          if (seats != 2)
            throw new ArgumentException("Sports cars must have exactly 2 seats");
          break;
        case CarType.SUV:
          if (seats < 5)
            throw new ArgumentException("SUVs must have at least 5 seats");
          break;
      }

      car.Seats = seats;
      return this;
    }

    public Car Build() => car;
  }

  public static ISpecifyCarType Create()
  {
    return new Impl();
  }
}
```

#### Functional Builder

```csharp
var person = new PersonBuilder()
    .Called("John")
    .WorksAsA("Developer")
    .Build();
```

```csharp
public class Person
{
  public string Name;
  public string Position;
}
public abstract class FunctionBuilder<TSubj, TSelf>
  where TSelf : FunctionBuilder<TSubj, TSelf>
  where TSubj : new()
{
  private readonly List<Func<TSubj, TSubj>> actions = new();

  public TSelf Do(Action<TSubj> action) => AddAction(action);

  public TSubj Build() => actions.Aggregate(new TSubj(), (p, f) => f(p));

  private TSelf AddAction(Action<TSubj> action)
  {
    actions.Add(p =>
    {
      action(p);
      return p;
    });
    return (TSelf)this;
  }
}

public sealed class PersonBuilder : FunctionBuilder<Person, PersonBuilder>
{
  public PersonBuilder Called(string name) => Do(p => p.Name = name);
}

public static class PersonBuilderExtensions
{
  public static PersonBuilder WorksAsA(this PersonBuilder builder, string position) =>
    builder.Do(p => p.Position = position);
}
```

#### Faceted Builder

```csharp
var person = new PersonBuilder()
    .Lives
      .At("123 London Road")
      .In("London")
      .WithPostcode("SW12BC")
    .Works
      .At("Fabrikam")
      .AsA("Engineer")
      .Earning(123000);
```

```csharp
public class Person
{

  public string StreetAddress, Postcode, City;


  public string CompanyName, Position;
  public int AnnualIncome;
}

public class PersonBuilder // facade
{
  protected Person person = new Person();

  public PersonJobBuilder Works => new PersonJobBuilder(person);
  public PersonAddressBuilder Lives => new PersonAddressBuilder(person);

  public static implicit operator Person(PersonBuilder pb)
  {
    return pb.person;
  }
}

public class PersonAddressBuilder : PersonBuilder
{
  public PersonAddressBuilder(Person person)
  {
    this.person = person;
  }

  public PersonAddressBuilder At(string streetAddress)
  {
    person.StreetAddress = streetAddress;
    return this;
  }

  public PersonAddressBuilder WithPostcode(string postcode)
  {
    person.Postcode = postcode;
    return this;
  }

  public PersonAddressBuilder In(string city)
  {
    person.City = city;
    return this;
  }
}

public class PersonJobBuilder : PersonBuilder
{
  public PersonJobBuilder(Person person)
  {
    this.person = person;
  }

  public PersonJobBuilder At(string companyName)
  {
    person.CompanyName = companyName;
    return this;
  }

  public PersonJobBuilder AsA(string position)
  {
    person.Position = position;
    return this;
  }

  public PersonJobBuilder Earning(int annualIncome)
  {
    person.AnnualIncome = annualIncome;
    return this;
  }
}
```

### Factory Method and Abstract Factory

#### Factory Method

Construcotr not descriptive enough or need overloads

```csharp
var point = Point.NewPolarPoint(1.0, Math.PI / 2);
```

```csharp
public class Point
{
  private double x, y;

  private Point(double x, double y)
  {
    this.x = x;
    this.y = y;
  }

  public static Point NewCartesianPoint(double x, double y)
  {
    return new Point(x, y);
  }

  public static Point NewPolarPoint(double rho, double theta)
  {
    return new Point(rho * Math.Cos(theta), rho * Math.Sin(theta));
  }
}
```

#### Async Factory

```csharp
public class Person
{
  public string Name;
  public string Position;

  public async static Task<Person> NewPersonFromJsonAsync(string json)
  {
    var person = JsonConvert.DeserializeObject<Person>(json);
    return await Task.FromResult(person);
  }
}
```

#### Object Tracking and bulk replacement

```csharp
public interface ITheme
{
  string TextColor {ger;}
  string BackgroundColor {get;}
}

public class LightTheme : ITheme
{
  public string TextColor => "Black";
  public string BackgroundColor => "White";
}

public class DarkTheme : ITheme
{
  public string TextColor => "White";
  public string BackgroundColor => "Black";
}

public class TracikngThemeFactory
{
  private readonly List<WeakRefence<ITheme>> themes = new();

  public ITheme CreateTheme(bool dark)
  {
    ITheme theme = dark ? new DarkTheme() : new LightTheme();
    themes.Add(new WeakReference<ITheme>(theme));
    return theme;
  }
}
```

## Structural Patterns

- concerned with structure (e.g. class members)
- imporatnce for good API design

## Behavioral Patterns

- everything else
