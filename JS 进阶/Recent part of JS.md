## Iterators & Generators

```js
var str = "Hello"
var world = ["W", "o", "r", "l", "d"]

var it1 = str[Symbol.iterator]()
var it2 = world[Symbol.iterator]()

it1.next() // { value: "H", done: false }
it1.next() // { value: "e", done: false }
it1.next() // { value: "l", done: false }
it1.next() // { value: "l", done: false }
it1.next() // { value: "o", done: false }
it1.next() // { value: undefined, done: true }

it2.next() // { value: "W", done: false }
// ..
```

```js
var str = "Hello"

var letters = [...str]
letters
// ["H", "e", "l", "l", "o"]
```

`for(let v of x) ` 会尝试去访问 `symbol.iterator` ，如果没有就不能遍历，一般 object 就没有

```js
var obj = {
    a: 1,
    b: 2,
    c: 3,
    [Symbol.iterator]: function () {
        var keys = Object.keys(this)
        var index = 0
        return {
            next: () => 
            	(index < keys.length) ?
            		{ done: false, value: this[keys[index++]] } :
            		{ done: true, value: undefined }
        }
    }
}
```

```js
var obj = {
    a: 1,
    b: 2,
    c: 3,
    *[Symbol.iterator]() {
        for (let key of Object.keys(this)) {
            yield this[key]
        }
    }
}

[...obj] // [1, 2, 3]
```

