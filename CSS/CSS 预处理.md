网站复杂度日益加深，CSS 语法能力不够

## 嵌套

```css
// css
.link-list {
    border-radius: 10px;
}
.link-list .link-item {
    text-decoration: none;
}
.link-list .link-item:hover {
    background-color:  #188eee；
}
```

```scss
// scss
.link-list {
    border-radius: 10px;
    
    .link-item {
        text-decoration: none;
        
        &:hover {
            background-color:  #188eee；
        }
    }
}
```

## 变量

```css
.tt {
  color: #188eee;
}

.link {
  color: #188eee;
}

.btn {
  color: #188eee;
}
```

    $blue: #188eee;
    .tt {
        color: $blue;
    }
    .link {
        color: $blue;
    }
    .btn {
        color: $blue;
    }
## mixin

```css
.box {
  -moz-box-shadow: 2px 4px #ccc;
  -webkit-box-shadow: 2px 4px #ccc;
  box-shadow: 2px 4px #ccc;
}

.header {
  -moz-box-shadow: 3px 4px #eee;
  -webkit-box-shadow: 3px 4px #eee;
  box-shadow: 3px 4px #eee;
}
```

```scss
@mixin box-shadow($shadow...) {
  -moz-box-shadow: $shadow;
  -webkit-box-shadow: $shadow;
  box-shadow: $shadow;
}

.box {
  @include box-shadow(2px 4px #ccc);
}

.header {
  @include box-shadow(3px 4px #eee);
}
```

## 循环

```css
.item-1 {
  background-image: url(xxx/1.png);
}

.item-2 {
  background-image: url(xxx/2.png);
}

.item-3 {
  background-image: url(xxx/3.png);
}
```

```scss
@for $i from 1 to 4 {
  .item-#{$i} {
    background-image: url(xxx/#{$i}.png);
  }
}
```

