### A Developer's journey from OO to Functional
#### [@ReidNEvans](http://twitter.com/reidnevans)
#### http://reidevans.tech
#### [@FunctionalKnox](http://twitter.com/functionalknox)

---

Overview

* OO design patterns
* SOLID principles
* Code Bloat
* Staying focused on the happy path
* Anecdotal Results

---

<!-- .slide: class="quote" -->
> The model you use to view the world shapes the thoughts you are able to think.

@TheBurningMonk

---


My beginnings in professional development

```
procedure TForm1.Button1Click(Sender: TObject);
begin
	Label1.Caption := 'Hello World';
end;
```


---

```
procedure TForm1.Button1Click(Sender: TObject);
begin
	Locations := TTable.Create(Self);
	with Locations do
	begin
		DatabaseName := 'MyDB';
		TableName := 'MyTable';
		Open;
		
		Append;
		FieldByName('City').Value := cityEdit.Text;
		FieldByName('State').Value := stateEdit.Text;
		Post;
	
		Close;			
	end;
end;
```

---

```
void button1_Click(object sender, System.EventArgs e)
{
	using (var con = new SqlConnection("MyDb"))
	using (var command = con.CreateCommand())
	{
		command.CommandText = 
			@"Insert into Locations ([State], City)
			  Values(@State, @City)";
			  
		command.Parameters.AddWithValue("City", textboxCity.Text);
		command.Parameters.AddWithValue("State", textboxState.Text);  
			  
		con.Open();
		command.ExecuteNonQuery();
	}		
}
```

---

<!-- .slide: class="question" -->
What if I want to do the same thing from a different event?

---

```
void button1_Click(object sender, System.EventArgs e)
{
	CreateLocation();
}

void CreateLocation()
{
	using (var con = new SqlConnection("MyDb"))
	using (var command = con.CreateCommand())
	{
		command.CommandText = 
			@"Insert into Locations ([State], City)
			  Values(@State, @City)";
			  
		command.Parameters.AddWithValue("State", tbState.Text);
		command.Parameters.AddWithValue("City", tbCity.Text);  
			  
		con.Open();
		command.ExecuteNonQuery();
	}
}
```

---

````
public class LocationsManager  
{
	public void CreateLocation(string city, string state) 
	{
		if (!States.Contains(state)) throw new ArgumentException()
		
		using (var con = new SqlConnection("MyDb"))
		using (var command = con.CreateCommand()) {
			command.CommandText = 
				@"Insert into Locations ([State], City)
				  Values(@State, @City)";
				  
			command.Parameters.AddWithValue("State", state);
			command.Parameters.AddWithValue("City", city);  
				  
			con.Open();
			command.ExecuteNonQuery();
		}
	}
}
```

---

<!-- .slide: class="question" -->
How should all this code fit together?

---

### OO Design patterns

---

Factory 

```
public class Bar
{
	public IBeverage MakeDrink(Person person)
	{
		switch (person)
		{
			case Person.TheDude : return new Caucasian();
			case Person.JackieTreehorn : return new HellOfACaucasian();
			default: return new WhiteRussian();
		}
	}
}
```

---

Strategy

```
interface IBowler
{
	bool Rolls(DateTime date);
}

class WalterSobchak : IBowler
{
	public bool Rolls(DateTime date)
	{
		return !date.IsSabbath;
	}
}
```

---

<!-- .slide: class="quote" -->
Design patterns are just terms to define how an abstraction is passed around in code

---

<!-- .slide: class="question" -->
How do I know if the code I'm writing is any good?

---

## Solid principles

* Single Responsibility Principle
* Open Closed Principle
* Liskov Substitution Principle
* Interface Segregation Principle
* Dependency Inversion Principle

---

<!-- .slide: class="question" -->
Can't I do all of that in Object Oriented code?

---

```
public class LocationsManager  
{
	public void CreateLocation(string city, string state) 
	{
		if (!States.Contains(state)) throw new ArgumentException()
		
		using (var con = new SqlConnection("MyDb"))
		using (var command = con.CreateCommand()) {
			command.CommandText = 
				@"Insert into Locations ([State], City)
				  Values(@State, @City)";
				  
			command.Parameters.AddWithValue("State", state);
			command.Parameters.AddWithValue("City", city);  
				  
			con.Open();
			command.ExecuteNonQuery();
		}
	}
}
```

---

```
public class LocationsManager  
{
	readonly ILocationRepository _locationRepository;
	readonly IStateValidator _stateValidator;
	
	public LocationsManager(ILocationRepository r, IStateValidator v)
	{
		_locationRepository = r; 
		_stateValidator = v;
	}

	public void CreateLocation(string city, string state)
	{
		if (!_stateValidator.IsValid(state)) 
			throw new ArgumentException("Not a valid State");
		_locationRepository.Insert(city, state);
	}
}
```

---

```
public interface ILocationRepository
{
	void Insert(string city, string state);
}

public class LocationRepository : ILocationRepository
{
	public void Insert(string city, string state)
	{
		using (var con = new SqlConnection(Global.ConnectionString))
		using (var command = con.CreateCommand()) {
			command.CommandText = 
				@"Insert into Locations ([State], City)
				  Values(@State, @City)";
				  
			command.Parameters.AddWithValue("State", state);
			command.Parameters.AddWithValue("City", city);  
			con.Open();
			command.ExecuteNonQuery();
		}
	}
}	
```

---

```
public interface IStateValidator
{
	bool IsValid(string state);
}

public class StateValidator : IStateValidator
{
	static readonly List<string> ValidStates = new List<string>
	{
		"AL", "AK", "AZ" //etc..
	};
	
	public bool IsValid(string state)
	{
		return ValidStates.Contains(state);
	}
}
```

---

```
public class ServiceModule : NinjectModule
{
	public override void Load()
	{
		Bind<ILocationRepository>().To<LocationRepository>();
		Bind<IStateValidator>().To<StateValidator>();
	}
}
```

---

```
public class LocationsManager  
{
	readonly ILocationRepository _locationRepository;
	readonly IStateValidator _stateValidator;
	
	public LocationsManager(ILocationRepository r, IStateValidator v)
	{
		_locationRepository = r; 
		_stateValidator = v;
	}

	public void CreateLocation(string city, string state)
	{
		if (!_stateValidator.IsValid(state)) 
			throw new ArgumentException("Not a valid State");
		_locationRepository.Insert(city, state);
	}
}
```

---

<!-- .slide: class="quote" -->
Code Complete by Steve McConnell

> **Industry Average**: about 15 - 50 errors per 1000 lines of delivered code.

<br>

> **Microsoft Applications**: about 10 - 20 defects per 1000 lines of code during in-house testing

---

<!-- .slide: class="question" -->
Can we stay SOLID without code bloat?

---

<!-- .slide: class="quote" -->
>If you have a class with two methods and one of them is the constructor you have a function

@JackDied

---

```
public class LocationsManager  
{
	readonly ILocationRepository _locationRepository;
	readonly IStateValidator _stateValidator;
	
	public LocationsManager(ILocationRepository r, IStateValidator v)
	{
		_locationRepository = r; 
		_stateValidator = v;
	}

	public void CreateLocation(string city, string state)
	{
		if (!_stateValidator.IsValid(state)) 
			throw new ArgumentException("Not a valid State");
		_locationRepository.Insert(city, state);
	}
}
```

---

```
let createLocation insert isValid city state =
    if state |> isValid
    then insert city state
    else raise (System.ArgumentException("Not a valid State"))
```

---

<!-- .slide: class="question" -->
What is Functional Programming?

---

![DomainCodomain](images/domain-codomain.png)

---

## Totality

Every element in the domain must be mapped to some element in the codomain 

---

## Determinism

Calling a function with the same value (in domain) results in same value (in codomain).

---

```fsharp
let double x = x * 2
```

---

```
function greet(who) {
	console.log("hello " + who)
}
```

---

```fsharp
let formatDate format =
    DateTime.Now.ToString(format)
```

---

```csharp
public string FormatDate(DateTime date, string format)
{
	return date.ToString(format);
}
```

---

```fsharp
let divide x y = x / y
```

---

```fsharp
let divide x y =
    if y = 0
    then None
    else Some (x / y)
```

---

<!-- .slide: class="quote" -->
Every function can be replaced with any other function that has the same signature

---

```csharp
interface ICalculator { int Calculate(int input); }

class LoggingCalculator: ICalculator
{
   ICalculator _innerCalculator;
   LoggingCalculator(ICalculator innerCalculator)
   {
	  _innerCalculator = innerCalculator;
   }

   public int Calculate(int input) 
   { 
	  Console.WriteLine("input is {0}", input);
	  var result  = _innerCalculator.Calculate(input);
	  Console.WriteLine("result is {0}", result);
	  return result; 
   }
}
```

---

Equivalent JS

```javascript
var log = function(func, input) {
	console.log(input);
	var result = func(input);
	console.log(result);
	return result;
}
```

---

Statically checked types in C#

```csharp
public static TResult Log<T, TResult>(Func<T, TResult> func, T input)
{
	Console.WriteLine("input is " + input.ToString());
	var result = func(input);
	Console.WriteLine("result is " + result.ToString());
	return result;
}
```

The same code in F#

```fsharp
let log func input = 
    printfn "input is %A" input
    let result = func input
    printfn "result is %A" result
    result
```

---

You're already using these techniques!

```csharp
var strings = new List<string>{ "hello", "world" };

string ToUpper(string s) {
	return s.ToUpper();
}

int Length(string s) {
	return s.Length;
}

strings.Select(ToUpper); // "HELLO", "WORLD"
strings.Select(Length); // 5, 5
```

---

Select definition

```csharp
static IEnumerable<U> Select<T>(this IEnumerable<T> ts, Func<T, U> mapper)
```

---

<!-- .slide: class="quote" -->
Staying focused on the happy path

---

Happy Path in OO / Imperative 

```csharp
public void InsertLocation(Location location)
{
	validateRequest(location);
	db.updateDbFromRequest(location);
	email.EmailNearbyCustomers(location);
}
```

---

```csharp
public void InsertLocation(Location location)
{
	if (!validateRequest(location))
		throw new ArgumentException("Location not valid");
	db.updateDbFromRequest(location);
	email.EmailNearbyCustomers(location);
}
```

---

```csharp
public void InsertLocation(Location location)
{
	if (!validateRequest(location))
		throw new ArgumentException("Location not valid");
	try
	{
		db.updateDbFromRequest(location);
	} 
	catch (Exception e)
	{
		Logger.Log(e);
		throw;
	}	
	email.EmailNearbyCustomers(location);
}
```

---

Happy Path with FP

```csharp
IMaybe<Location> InsertLocation(Request x) {
	return ValidateRequest(x)
		.Chain( UpdateDbFromRequest )
		.Chain( EmailNearbyCustomers )
}
```

---

Unhappy Path in FP

```csharp
IMaybe<Location> InsertLocation(Request x) {
	return ValidateRequest(x)
		.Chain( UpdateDbFromRequest )
		.Chain( EmailNearbyCustomers )
}
```

---

![inconceivable](images/inconceivable.gif)

---

Remember Pure Functions?

```fsharp
// Impure
let divide x y = x / y

// Pure
let divide x y =
    if y = 0
    then None
    else Some (x / y)
```

---

Encoding Logic flow into the type system
```csharp
IMaybe<Location> InsertLocation(Request x) {
	return ValidateRequest(x)
		.Chain( UpdateDbFromRequest )
		.Chain( EmailNearbyCustomers )
}
```

---

Imperatively branching
```csharp
public void InsertLocation(Location location)
{
	if (!validateRequest(location))
		throw new ArgumentException("Location not valid");
	try
	{
		db.updateDbFromRequest(location);
	} 
	catch (Exception e)
	{
		Logger.Log(e);
		throw;
	}	
	email.EmailNearbyCustomers(location);
}
```

---

C# .NET 4.5
	
```csharp
async Task<int> AddSomeNumbers()
{
	int one = await Task.FromResult(1);
	int two = await Task.FromResult(2);
	return one + two;
}
```

Without Syntatic Sugar	
```csharp
async Task<int> AddSomeNumbers()
{
	return Task.FromResult(1)
		.Chain(x => Task.FromResult(x + 2))
}
```

---

<!-- .slide: class="question" -->
How do I choose a functional language?

---

* **Clojure** - A Lisp on the JVM
* **Elm** - Pure language for the browser
* **Erlang / Elixir** - Massive scalability
* **F#** - Functional first on CLR / .NET
* **Haskell** - Pure functional language 
* **Scala** - Functional first on the JVM
* **Your Current Language**

---

## Anecdotal Results of switching to FP

* Social dealer management system for Freightliner
* 5 months development
* 3,582 lines of code
* 2 bugs not caught at compile time

---


<!-- .slide: class="quote" -->
> The model you use to view the world shapes the thoughts you are able to think.

@TheBurningMonk

---

###[@ReidNEvans](http://twitter.com/reidnevans)

####http://reidevans.tech
####[@FunctionalKnox](http://twitter.com/functionalknox)
