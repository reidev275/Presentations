### The 4 steps to becoming a Functional Programmer
#### [@ReidNEvans](http://twitter.com/ReidNEvans)
#### http://reidevans.tech

---

###Functional Programming is taking over

---

![wantToLearn](images\wantToLearn.png)


<sub>https://medium.com/@sachagreif/the-state-of-javascript-front-end-frameworks-1a2d8a61510#.cl0ytej2r</sub>

---

###How do you get started?

---

1. Be open to a new approach
2. No more loops
3. Stop mutating state
4. Embrace purity

---

###Be open to a new approach

---

![bluebox](images\bluebox.jpg)

---

![greenbox](images\greenbox.jpg)

---

###No more loops

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
const isEven = x => x % 2 == 0,
      timesTwo = x => x * 2;

[1,2,3,4].filter(isEven)
         .map(timesTwo)

timesTwo(2)
  
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

###Stop Mutating State

---


