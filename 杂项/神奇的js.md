

```js
(function() {
  (0,eval)("var foo = 123"); // indirect call to eval, creates global variable
})();
console.log(foo);            // 123
(function() {
  eval("var bar = 123");     // direct call to eval, creates local variable
})();
console.log(bar);            // ReferenceError
```

