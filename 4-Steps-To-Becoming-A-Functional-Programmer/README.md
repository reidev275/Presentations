### The 4 steps to becoming a Functional Programmer
#### [@ReidNEvans](http://twitter.com/ReidNEvans)
#### http://reidevans.tech
#### [@FunctionalKnox](http://twitter.com/FunctionalKnox)

---

* A strong desire to be overly vocal to anyone who might possibly listen to you
* Wanting to look down upon other developers (bonus points for fans of
  towers, ivory, or neck hair)
* Being unhappy working in any language someone might actually
  pay you to use
* wanting to write overly academic code that has no value to real world business
  problems

---

<!-- .slide: class="feature" -->
###Functional Programming is taking over

---

![wantToLearn](images\wantToLearn.png)


<sub>https://medium.com/@sachagreif/the-state-of-javascript-front-end-frameworks-1a2d8a61510#.cl0ytej2r</sub>

---

Top wanted tech (via StackOverflow)

![wanted](images\wanted.png)

---

Top wanted tech (via StackOverflow)

![wantedFP](images\wantedFP.png)

---

Top Paying tech (via StackOverflow)

![topPaying](images\topPaying.png)

---

Top Paying tech (via StackOverflow)

![topPayingDataRemoved](images\topPayingDataRemoved.png)

---

<!-- .slide: class="feature" -->
###How do I get started?

---

1. Be open to a new approach
2. No more loops
3. Stop mutating state
4. Embrace purity

---

###1. Be open to a new approach

---

![bluebox](images\bluebox.jpg)

---

![greenbox](images\greenbox.jpg)

---

###2. No more loops

---

```js
var list = [1,2,3,4],
result = [];
        
for (var i = 0; i < list.length; i++) {
    if (list[i] % 2 === 0) {
        result.push(list[i] * 2);
    }
}
```

---

```js
[1,2,3,4].filter(x => x % 2 == 0)
         .map(x => x * 2)
```

---

```js
const isEven = x => x % 2 == 0,
      timesTwo = x => x * 2;
        
[1,2,3,4].filter(isEven)
         .map(timesTwo)
```

---

```js
const timesTwo = x => x * 2;

timesTwo(2)

[1,2,3,4].map(timesTwo)
  
$.when(2)
.then(timesTwo)
```

---

```js
const isAdmin = x => x.level > 9 && x.isActive;

function getAdmins() {
    return $.getJson('/api/users')
            .then(data => data.filter(isAdmin))
}
```

---

<!-- .slide: class="quote" -->

> "Being abstract is something profoundly different from being vague … The purpose of abstraction is not to be vague, but to create a new semantic level in which one can be absolutely precise." 

Dijkstra

---

###3. Stop Mutating State

---

<!-- .slide: class="feature" -->

Mutating state makes code harder to equate

---

```csharp
class Person
{
  public string Name { get; set; }
  public int Age { get; set; }
}

var reid = new Person
{
  Name = "Reid",
  Age = 35 
}
var alsoReid = new Person
{
  Name = "Reid",
  Age = 12
}
```

---

```csharp
class Person
{
  public Person(string name, DateTime birthdate)
  {
    Name = name;
    Birthdate = birthdate;
  }
  public string Name { get; private set; }
  public DateTime Birthdate { get; private set; }
}
```

---

<!-- .slide: class="feature" -->

Mutating state makes code

* Harder to test
* Harder to debug
* Harder to parallelize

---

```js
var two = 2;

$.get('/numberOfTheDay')
.then(x => two += x);

console.log(two)
```

---

```js
const two = 2;

$.get('/numberOfTheDay')
 .then(x => two + x)
 .then(console.log);
```

---

###4. Embrace Purity 

---

```js
var z = 10;

function add(x, y) {
    return x + y;
}
```

---

```js
function five() {
  return 5;
}
```

---

```js
function timesTwo(x) {
  return x * 2;
}
```

---

```js
const timesTwo = x => x * 2;

timesTwo(2)

[1,2,3,4].map(timesTwo)
  
$.when(2)
.then(timesTwo)
```

---

Additional Resources

* Simple Made Easy - Rich Hickey
* Practical Functional Programming - James Coglan
* Stop Writing Classes - Jack Diederich
* [@ReidNEvans](http://twitter.com/reidnevans)
* [http://reidevans.tech](http://reidevans.tech)
* [@FunctionalKnox](http://twitter.com/functionalknox)
